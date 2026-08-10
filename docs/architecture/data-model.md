# Data model (Phase 1)

PostgreSQL + PostGIS. Three concerns share one database: the **registry** (M01, relational +
geometry), the **time series** (instruments + analytic results), and the **evidence ledger** (M14,
append-only). Mutable read models are projections over the ledger — see
[ADR 0004](0004-append-only-evidence-ledger.md).

This is a working sketch to seed the schema, not the final DDL.

## Registry (M01)

```
facility
  id, name, operator, ownership_chain
  lifecycle_phase        -- construction | operation | care_maintenance | decommissioning | closure | orphaned
  construction_method    -- upstream | centreline | downstream
  storage_method         -- slurry | thickened | paste | filtered_drystack | in_pit | backfill
  consequence_class      -- drives inspection frequency AND which analytics are offered
  -- geometry (PostGIS, SRID 4326):
  centroid               geometry(Point)     -- lat/lng; defines the Sentinel-2 AOI centre (M05)
  aoi_bbox               geometry(Polygon)    -- bounding box the EO pipeline requests scenes for
  mgrs_tile              text                 -- Sentinel-2 tile id covering the AOI (e.g. 35JPM)
  crest_polygon          geometry(Polygon)
  toe_line               geometry(LineString)
  calculation_boundary   geometry(Polygon)   -- crest-line polygon bounding pond-area computation (M05)
  beach_extent           geometry(Polygon)
  decant_structures      geometry(MultiPoint)
  downstream_receptors    geometry(MultiPolygon)

design_criteria           -- the freeboard STACK (per ADR 0002); every allowance is its own field
  facility_id
  min_decant_storage_allowance     -- LOWER bound: water must stay above this after discharge
  wet_season_storage_allowance
  extreme_storage_allowance
  contingency_storage_allowance
  flood_spill_depth
  max_operational_level
  operational_freeboard            -- crest to top of tailings
  design_beach_length, design_beach_slope, target_fos
  review_date                      -- design-storm assumptions are non-stationary; not fixed for life

instrument
  id, facility_id, type            -- piezometer | inclinometer | survey_monument | gnss | flow_meter | turbidity | weather | cctv
  current_position  geometry(Point)
  status                           -- live | offline | relocated | decommissioned
instrument_position_history        -- a monument that MOVED must be distinguishable from ground that moved
  instrument_id, position geometry(Point), effective_from, effective_to, moved_by, ledger_event_id

role_assignment
  facility_id, role                -- responsible_executive | engineer_of_record | sheq_manager
  person, contact, effective_from  -- reassignment is a ledger event

inspection_plan
  facility_id
  level                            -- routine | intermediate | comprehensive | special | emergency
  required_frequency               -- indexed to consequence_class
```

## Users & inspectors (onboarding)

Two populations. **Management users** authenticate into the console (role-based). **Inspectors**
never log in — they are identified by their WhatsApp number (ADR 0006).

```
app_user                          -- management console access
  id, name, email
  role                            -- sheq_manager | engineer_of_record | responsible_executive | auditor
  status                          -- invited | active
  facility_ids[]                  -- which facilities they can see

inspector                         -- WhatsApp-only; no login
  id, name
  whatsapp_number                 -- unique identity
  facility_id                     -- assigned facility / route
  opt_in_at                       -- WhatsApp requires explicit opt-in
  status                          -- invited | active
  competency                      -- inspector competency record (standards requirement)

wa_message                        -- every inbound/outbound WhatsApp message (chain of custody)
  id, provider_msg_id, direction  -- in | out
  inspector_id, received_at
  body, media_s3_key              -- photo pulled from provider → S3
  parsed_into                     -- inspection_id | alert_id | null (unparsed → console review)
  ledger_event_id
```

The bot turns an inbound photo/reply into structured records: a `wa_message` → an `inspection`
(scheduled round) or an `alert` (ad-hoc photo), and always a `ledger_event`.

## Job cards (M13) — dispatch + WhatsApp automation

Management issues a **job card** (a work order — a scheduled round, an alert follow-up/CAPA, or an
ad-hoc task). Dispatch sends it to the assigned inspector over WhatsApp; the bot then runs the
reminder + receive-image automation and the status flows back.

```
job_card
  id, facility_id, scope, source            -- schedule | alert | adhoc
  inspector_id                              -- assigned; dispatched to their whatsapp_number
  due_at, priority                          -- routine | elevated | urgent (tied to failure-mode severity)
  status                                    -- dispatched | reminded | in_progress | completed (+ overdue flag)
  from_alert_id                             -- set when source = alert (CAPA link)
  ledger_event_id                           -- job_card_issued / job_reminder / inspection_received / job_card_completed

-- automation (Celery/APScheduler jobs, per card):
--   T-2h no response      → send reminder            (job_reminder)
--   past due              → nudge + escalate to SHEQ  (escalation)
--   inbound photo         → ingest media → in_progress (inspection_received)
--   round parsed          → inspection + close        (job_card_completed)
```

Every job card message (out and in) is a `wa_message` and a `ledger_event`, so the dispatch →
reminder → photo → close lifecycle is fully auditable.

## Inspection (M03b)

```
inspection
  id, facility_id, level, inspector, started_at, submitted_at
  status                           -- draft | submitted | amended
  route_from_geometry              -- ordered points from M01 so the round has a defined end
inspection_category                -- 7 per the canonical taxonomy
  inspection_id, category          -- tailings_surface | embankment | seepage | ancillary | emergency_prep | instrumentation | misc
  all_normal boolean               -- default true; false expands parameters
inspection_parameter
  category_id, parameter, ordinal_value, note
photo
  parameter_id | inspection_id, s3_key, gps geometry(Point), captured_at, prior_photo_id
inspection_amendment               -- never overwrites; references the original (ADR 0003)
  inspection_id, field, old_value, new_value, amended_by, amended_at, ledger_event_id
```

Schedule adherence ("next due", "overdue", weekly rate) is a **projection** computed from
`inspection_plan` × submitted `inspection` rows — a first-class home-screen feature, not a report.

### Structured observation (M03b + M08) — quantifying human judgment

A free-text finding ("new seepage at the west wall toe") is **not** a storable output — it is not
comparable across inspectors, not trendable, and cannot be corroborated automatically. Every flagged
finding is instead captured on a **standardised closed-ended schema**: the same ordinal parameters
(0 best → 3 worst), scored the same way by every inspector, producing a single weighted **Observation
Severity Index (OSI, 0–100)** plus a confidence and a MAC-style band. The eye still raises the finding;
the schema makes it a number.

```
observation                          -- one flagged finding, standardised
  id, inspection_id, alert_id        -- raised from a round or an ad-hoc WhatsApp photo
  category                           -- seepage | embankment | tailings_surface | ancillary | instrumentation | ...
  inspection_point_id                -- WHERE (M01 GPS point, e.g. IP-02 "west wall toe") — not free text
  observed_by, observed_at, method   -- e.g. "visual + reference photo"
  confidence                         -- 0–100, AQI-style (visibility + access + photo reference)
  severity_index, severity_band      -- DERIVED: OSI + Low|Moderate|Elevated|High (see below)
  failure_mode_id, tarp_id           -- binds the finding to the FMEA mode + TARP register it feeds
  ledger_event_id                    -- observation_recorded

observation_parameter                -- the closed-ended scores; category defines the parameter set
  observation_id, parameter          -- e.g. occurrence | water_colour | clarity | flow_rate | extent
  ordinal_value                      -- 0..3 on a NAMED scale ("None|Existing|Changed|New")
  weight                             -- per-parameter weight (turbidity/occurrence weigh heaviest — piping signal)
  comparator                         -- absolute | vs_last_round | vs_design  (the MAC comparator basis)
  measured_note                      -- optional hard number where available ("≈0.5 L/min est.")

-- Observation Severity Index:  OSI = round( 100 * Σ(ordinal·weight) / Σ(3·weight) )
--   band: <25 Low · 25–49 Moderate · 50–74 Elevated · ≥75 High
-- The parameter scales per category are a versioned reference table (a scale change is a ledger event),
-- so an OSI is reproducible and two inspectors scoring the same wall land within one ordinal step.
```

`observation` closes the objective/subjective loop: the OSI is cross-checked against the objective
streams (piezometer trend, drone erosion overlay, satellite pond centroid) to yield a corroboration
count, and only then escalates to a job card (M13) / the Engineer of Record.

## Analytics (M05)

```
scene                              -- Sentinel-2 acquisition + QA
  id, facility_id, sensed_at, cloud_pct, rejected boolean, rejection_reason
site_calibration                   -- audit evidence, NOT a config value (ADR 0005)
  facility_id, ndwi_threshold, nir_threshold
  transect geometry(LineString), validation_dates[], residual_error, derived_by, ledger_event_id
analytic_result
  id, facility_id, scene_id, published_at
  pond_area, pond_area_uncertainty
  pond_centroid geometry(Point), min_distance_to_wall    -- the Merriespruit mechanism, measurable
  min_water_to_crest, beach_area                          -- upstream/centreline only
  freeboard_status jsonb                                   -- per allowance, upper AND lower bound
  fitness_for_purpose                                      -- publishable | refused (error > configured fraction)
```

## Drone / UAV (fourth stream — ADR 0007)

```
drone_flight
  id, facility_id, flown_at, pilot, type      -- photogrammetry | erosion_mapping | video
  products                                     -- orthomosaic_s3, dem_s3, video_s3 (GPS ground control)
  beach_distance_min_m, findings jsonb         -- e.g. rills detected → alert / job_card
  ledger_event_id                              -- drone_flight
```

## Reports & cost telemetry (ADR 0008)

```
report
  id, template, version, cadence               -- daily_brief | weekly_adherence | monthly_technical
                                               -- | gistm_topic6 | regulator_pack
  generated_at, ledger_head_hash               -- reproducible: built from the chain head it records
  recipients[], s3_key, ledger_event_id        -- report_generated

cost_entry                                     -- the OR/maintenance dataset, per facility per month
  facility_id, period, stream                  -- satellite | drone | iot | manual
  unit_cost, volume, observation_count         -- → cost_per_observation is derived
```

## Evidence ledger (M14)

```
ledger_event                        -- APPEND ONLY. no UPDATE, no DELETE.
  id                                -- monotonic
  prev_hash, hash                   -- hash-chained; hash = H(prev_hash || canonical(payload))
  actor, occurred_at, client_time   -- client_time preserved for offline-captured events
  type                              -- inspection_submitted | inspection_amended | photo_captured
                                    -- | analytic_published | threshold_created | threshold_changed
                                    -- | alert_raised | alert_acknowledged | alert_dismissed
                                    -- | instrument_relocated | design_criterion_modified | role_reassigned
  payload jsonb                     -- includes who + why for threshold_changed and alert_dismissed
  refs                              -- corrections reference prior event ids
```

**Access rule:** application role has `INSERT`/`SELECT` on `ledger_event`; `UPDATE`/`DELETE` are
revoked at the database grant level, not just in code. Projections (registry read models, adherence,
current thresholds) are rebuildable from the log.
