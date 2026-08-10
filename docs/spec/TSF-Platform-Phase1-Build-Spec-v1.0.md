# TSF Monitoring Platform — Phase 1 Build Specification
## Pilot release, v1.0

**Companion document:** *TSF Monitoring Platform — Module Architecture v0.4*. That document is the reasoning and the source provenance. This one is the build.

---

## 1. Constraints this spec is written against

| Constraint | Value | Consequence |
|---|---|---|
| Team | 1 full-stack developer, geospatial-comfortable | Scope is cut to four deliverables. Nothing else ships. |
| Timebox | ≤3 months to working pilot | No InSAR. No ML. No multi-tenant. |
| Backend | Python | FastAPI + PostgreSQL/PostGIS + object storage |
| Field client | PWA (see §7) | Native wrappers deferred to Phase 2 via Capacitor |
| Hosting | Cloud, no residency constraint | Managed services throughout; do not self-host anything avoidable |
| Customer | Mine operator, paid pilot | Framing is operator assurance, not external audit |
| Daily user | Environmental / SHEQ manager | Not a geotechnical engineer. See §2. |
| Site | One facility, fully instrumented (piezometers, survey/GNSS, visual) | Ground truth available from day one |
| Documentation available | Design basis, facility geometry/survey, historic inspection records | M01 can be seeded properly |
| Success metric | **Inspections completed on schedule** | Everything below serves this |

**Scope discipline.** Four things ship: the asset registry, the inspection PWA, one satellite analytic, and the evidence ledger beneath both. If any of these is at risk, cut the satellite analytic first — it is the differentiator but the inspection app is the habit, and the habit is what converts the pilot.

---

## 2. The user, and what follows from it

The daily user is a **SHEQ manager**, not an Engineer of Record. This drives more design decisions than any other single fact.

**They own compliance evidence, incident reporting and audit readiness** across several obligations, of which tailings is one. They are accountable for whether the inspection happened, not for interpreting a deformation time series.

Therefore:

1. **No raw geotechnical output on a primary screen.** Every analytic surfaces as a plain-language status with a colour state. The underlying number and its uncertainty are one tap away, not on the front page.
2. **Escalation to the Engineer of Record is one action.** When something is beyond the SHEQ manager's competence — and the system should say so explicitly — routing it to a named engineer must take a single tap and must be logged.
3. **The system must never imply the SHEQ manager has cleared a geotechnical condition.** Acknowledging an alert is not a determination of safety. Wording matters here and is a liability question, not a UX preference.
4. **Compliance framing is native to them.** "3 of 4 inspections completed this week" is the language they already work in.

**"Faster than paper" is the adoption mechanism; "on schedule" is the pilot metric.** Speed causes adherence. Design for speed, measure adherence.

---

## 3. Deliverable 1 — M01 Asset Registry (seed from documentation)

Not a UI-heavy module. A well-modelled schema, seeded from the site documentation, that everything else resolves against.

**Must hold:**

- Facility identity, operator, lifecycle phase, **construction method** (upstream / centreline / downstream), **storage method** (slurry / thickened / paste / filtered)
- Geometry as PostGIS: crest polygon, toe, **calculation boundary** (crest-line polygon bounding pond-area computation), beach extent, decant structures, downstream receptors
- **Consequence classification** — drives inspection frequency
- **Design criteria from the design basis document**, specifically the freeboard stack:
  - minimum decant storage allowance
  - wet season storage allowance
  - extreme storage allowance
  - contingency storage allowance
  - flood spill depth
  - maximum operational level
  - operational freeboard (crest to top of tailings)
  - design beach length / slope, target FoS
- Instrument inventory with **position history** — a monument that moved must be distinguishable from ground that moved
- Named accountable roles: Responsible Executive, Engineer of Record, SHEQ manager
- **Inspection plan**: level (routine / intermediate / comprehensive / special / emergency) × required frequency, indexed to consequence class

**Design note.** Freeboard is a stack, not a number, and it has a **lower** bound as well as an upper one — water must stay above the minimum decant level after discharge. Over-drainage is a reportable deviation. Model all allowances as separate fields.

**Seeding is a data-archaeology task, not a coding task.** Budget real time for it with the in-house engineer. Facility type must be confirmed before §5 is built (see §9).

---

## 4. Deliverable 2 — M03b Inspection PWA *(the pilot's centre of gravity)*

This is what makes the operator open the system daily.

### 4.1 Form design — progressive, not exhaustive

The canonical inspection taxonomy has seven categories and ~40 parameters. **Presenting that as a 40-item form guarantees the pilot fails.** Instead:

- Each category renders as a single **"all normal"** control by default
- Tapping "all normal" clears the category in one action
- Flagging a category expands only that category's parameters
- **A clean inspection is a handful of taps. Only anomalies cost time.**

Categories (full parameter lists in Architecture v0.4 §M03b):

1. Tailings surface — pond size/position, freeboard, beach slopes, vortex
2. Embankment / berms — cracking, bulging, weeping, piping, sloughing, subsidence, sinkholes, erosion
3. Seepage flow — new/changed seepage, **colour of seepage water**, clarity, quantity
4. Ancillary infrastructure — decant integrity, concrete cracking, pumps, pipelines, drainage
5. Emergency preparedness — leak detection, flow measurement, alarm status
6. **Instrumentation** — station water levels, instrument condition and data quality
7. Miscellaneous — animal burrows

Note category 6: **the monitoring system is itself an inspected object.** This is not optional; it comes from the standards.

### 4.2 Colour and qualitative capture

Several parameters are colour-based (seepage water colour, water clarity, vegetation lushness). Free text is useless for trending. Capture as **structured photo + ordinal scale**, with the previous inspection's photo shown alongside for comparison.

### 4.3 Non-negotiable field requirements

- **Offline-first.** Service worker + IndexedDB. Full inspection round completable with no signal; sync on reconnect. A tailings facility does not have reliable coverage.
- **Camera-first.** Photo capture in ≤2 taps from any parameter. Auto-attach GPS and timestamp.
- **One-handed operation.** Large targets. Assume gloves, sun glare, a person holding a radio.
- **Smart defaults.** Pre-populate from the previous inspection; the user confirms or changes.
- **Route guidance.** Ordered inspection points from M01 geometry so nothing is missed and the round has a defined end.
- **No silent edits.** Post-submission changes create an amendment record; they never overwrite.

### 4.4 Schedule adherence — the metric, instrumented

Because this is what the pilot is judged on, build it as a first-class feature, not a report:

- Inspection due/overdue state per facility per inspection level, computed from M01's plan
- Home screen leads with **"next due"** and **"overdue"**
- Weekly adherence rate as the headline figure
- Overdue inspections escalate to the SHEQ manager, then to the Responsible Executive
- **Baseline capture from the historic inspection records before go-live** — without a baseline the improvement claim is unprovable

---

## 5. Deliverable 3 — M05 Water, Beach & Freeboard analytic

One analytic, done properly, validated against instruments.

### 5.1 Pipeline

Sentinel-2 L2A surface reflectance (10 m, ~5-day revisit) via Earth Engine or equivalent. Cloud and cloud-shadow screening from scene QA metadata **plus visual confirmation**.

Two metrics, computed per pixel within M01's calculation boundary:

- **NDWI** = (Green − NIR) / (Green + NIR)
- **NIR reflectance** — low NIR indicates surface water; reported to give better water-edge contrast

### 5.2 Site-specific threshold calibration — mandatory, do not skip

There are **no universally applicable NDWI or NIR thresholds.** Procedure:

1. Define a transect crossing both beach and pond
2. Extract monthly profiles across a full year to capture seasonal variability
3. Establish conservative upper and lower bounds
4. Obtain independent very-high-resolution imagery; delineate the pond manually
5. Compute area mismatch AM = A_validation − A_satellite across candidate thresholds
6. Select the threshold minimising |AM|, weighted toward the low-sensitivity side of the mean curve
7. **Record the derivation, validation dates and residual error in the site calibration record** — this is audit evidence, not a config value

*Published reference values from a South African upstream platinum facility: NDWI bounds 0.05–0.40, adopted 0.11; NIR bounds 500–1000, adopted 870. Expected agreement within ±20% of validation area, absolute discrepancy reaching ~20 000 m². Use as a starting point only if the pilot facility is comparable — confirm facility type first.*

### 5.3 Derived outputs

- Pond area, with uncertainty band
- **Pond centroid and minimum distance to perimeter wall** — the Merriespruit mechanism, made measurable
- **Minimum water-to-crest distance** and beach area (upstream/centreline facilities only)
- Freeboard status against each M01 allowance, upper **and lower** bound

### 5.4 Publication discipline

- Every figure carries its uncertainty band
- Propagate pond-area error into derived beach and freeboard figures
- **Refuse to publish** where expected error exceeds a configured fraction of typical pond area
- Flag known edge cases: low pond level with exposed sandbanks; pond edges against gently sloping beach are inherently fuzzier than against a training wall

### 5.5 Cross-validation — the differentiator

Raw sensor access makes this achievable, and it is the thing the reference implementation in this field never managed. Ingest piezometer, survey/GNSS and decant level data. Publish satellite-derived pond metrics **alongside** instrument readings on a shared timeline.

This is the pilot's proof artefact. Build the comparison view deliberately.

---

## 6. Deliverable 4 — M14 ledger primitives *(build now, surface later)*

The full audit module is Phase 6. **The ledger cannot be retrofitted** and must exist from the first commit.

Append-only event store. Every one of these is an immutable event with actor, timestamp, and payload:

- Inspection submitted / amended
- Photo captured
- Analytic result published
- Threshold created or **changed** (including who and why)
- Alert raised / acknowledged / **dismissed with justification**
- Instrument relocated
- Facility design criterion modified
- Role reassigned

**Design constraints:** hash-chained records; no UPDATE or DELETE on the event table; all mutable state derived as a projection. Corrections are new events referencing prior ones.

**Operator framing.** For this customer the ledger is not surveillance — it is their **defence**. Demonstrable proof to their board, their Engineer of Record, their insurer and their lenders that monitoring occurred and action followed. Present it that way in the UI and in the contract.

---

## 7. Technical stack

| Layer | Choice | Note |
|---|---|---|
| API | Python, FastAPI | |
| Database | PostgreSQL + PostGIS | Managed. Geometry, time series, event store. |
| Geospatial processing | rasterio, GDAL, xarray, Earth Engine Python API | Native to the stack |
| Object storage | S3-compatible | Imagery, inspection photos |
| Task scheduling | Celery or APScheduler | Scene acquisition, threshold recomputation |
| Field client | **PWA** — service worker, IndexedDB, Web Camera/Geolocation | |
| Web client | Same codebase, responsive | |
| Auth | Managed provider | Role-based from day one |

**On mobile.** PWA covers offline capture, camera, GPS and home-screen install. The genuine native-only gaps — background sync, reliable push — are not Phase 1 requirements. Architect for a **Capacitor wrap in Phase 2** to reach app stores if operator IT policy requires it, reusing the same codebase. One developer cannot ship PWA plus two native clients in three months alongside everything above.

---

## 8. Explicitly out of scope for Phase 1

Recorded so the pilot contract does not accidentally promise them.

| Deferred | Why |
|---|---|
| **M04 InSAR** | Specialist pipeline — atmospheric correction, unwrapping QC, coherence management. Not credible greenfield in 3 months. Phase 2. |
| M06 chemical/vegetation analytics | Second analytic; adds no proof the first doesn't |
| M07 change detection | Phase 2, pairs with InSAR |
| M09 full TARP engine | Needs geotechnical sign-off per site; simple thresholds only in Phase 1 |
| M10 predictive models | No. See M18 — no model ships without governance, and there is no time for both. |
| M11 consequence modelling | Licence rather than build when the time comes |
| M12 full escalation | Overdue-inspection escalation only |
| M15 multi-stakeholder tiers | Single tenant |
| M17 full FMEA | **Capture the RPN baseline manually with the in-house engineer** — see §9 |
| M18 ML assurance | Nothing to govern yet |

---

## 9. Pre-build actions

Ordered. The first two block the spec.

1. **Confirm facility type.** Upstream/centreline slurry → §5 as written. Filtered or dry stack → **there is no supernatant pond and §5 is largely inapplicable**; Phase 1 must be re-cut around slope movement and seepage. *This is the one genuinely blocking unknown.*
2. **Resolve the base-rate discrepancy.** Two sources read Azam & Li (2010) incompatibly — 1.2% as an annual probability of failure versus 1.2% cumulative over ~100 years. Two orders of magnitude apart. Convergent incident counts favour the cumulative reading. **No QRA output, ROI claim or risk figure goes to the operator until this is settled from the primary source.**
3. **Close the data agreement**, covering: right to retain derived analytics and calibrated thresholds post-pilot; permission to use anonymised outcomes as a reference case; and **who holds the evidence ledger if the pilot does not convert.** Easier to secure now than later.
4. **Capture two baselines before go-live.** (a) Inspection schedule adherence from the historic records — the pilot metric is unprovable without it. (b) FMEA Detection scores per failure mode, scored with the in-house engineer. The platform moves the Detection axis; the pre-deployment score is the counterfactual, and it cannot be reconstructed after the fact.
5. **Acquire the standards.** GISTM full text is the highest-value item — Topic III is effectively this product's specification. Then MAC Tailings Guide and OMS Manual (free), CDA Dam Safety Guidelines, ICOLD Bulletin 158.

---

## 10. Suggested sequencing

| Weeks | Focus |
|---|---|
| 1–2 | Schema, PostGIS geometry, event store. Seed M01 from site documentation. Baselines captured. |
| 3–6 | Inspection PWA — offline capture, progressive forms, photo, sync. **The core deliverable.** |
| 7–8 | Schedule adherence, overdue logic, escalation. Instrument data ingestion. |
| 9–11 | Sentinel-2 pipeline. Threshold calibration against validation imagery. Cross-validation view. |
| 12 | Hardening, operator training, pilot review pack. |

**Week 6 checkpoint.** If the PWA is not in the operator's hands and in daily use by end of week 6, cut §5 to pond area only and protect the inspection app. The habit converts the pilot; the satellite analytic wins the renewal.
