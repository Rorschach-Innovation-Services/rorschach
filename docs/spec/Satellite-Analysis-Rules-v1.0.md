# Satellite-Analysis Rules — Standard v1.0

**Status:** Production standard for the developer handover
**Applies to:** the satellite (Earth-observation) stream (M05) and how its measured values become
graded results and plain-language statements.

## 1. Why this exists

In the prototype, the "what this means" interpretation was hand-written prose. That does not
scale and is not defensible: two people (or two facilities, or two passes) could describe the same
number differently. This standard replaces prose with **rules**.

A rule turns a **measured value** into a **band** (green / amber / red) and a **fixed statement**,
deterministically. The three properties that matter for production:

- **Standardised** — the same input always produces the same band and the same words. No judgement
  in the write-up; the judgement is in the rule, set once, reviewed, and versioned.
- **Scalable** — rules are *data*, not code. A new facility loads its own thresholds (or the
  defaults) and the *same* engine runs. No bespoke logic per dam.
- **Auditable** — every output cites the rule id, the version, the value, the band and the scene it
  was computed from. A reviewer can reproduce it.

> The developer implements the **engine and schema** in section 4–6. The **thresholds** are per-facility
> configuration, not code (section 7).

## 2. The rule (schema)

Each satellite observable is one rule.

```
satellite_rule
  id                 -- stable code, e.g. SAT-01
  version            -- ruleset version this belongs to (v1.0)
  facility_id        -- rules are per-facility (a global default row seeds new facilities)
  observable         -- human name, e.g. "Supernatant pond"
  metric             -- EXACT definition of what is measured + from which bands
                     --   e.g. "pond area as % of facility footprint, NDWI>0 largest water body"
  source_bands       -- the Sentinel-2 bands used, e.g. {B03,B08} (provenance)
  unit               -- %, ha, m, …
  comparator         -- absolute | vs_design | vs_baseline   (the MAC scale; see §5)
  direction          -- high_bad | low_bad                    (which way is worse)
  bands              -- { green: <num>, amber: <num> }        (site-set thresholds; red is beyond amber)
  binds              -- the failure mode(s)/TARP this feeds, e.g. "RA-01"
  statement          -- template per band { green|amber|red : { head, body } }
                     --   templates only; placeholders {v} {green} {amber}; NO free text
  applies_when       -- gating predicate over facility attrs (storage_method, consequence_class)
  active             -- bool
```

## 3. The observables (v1.0 baseline set)

Default thresholds shown; each facility may override. All four are demonstrated live on the
Satellite operations page against Parabola TSF1 (3 Jul 2026).

| ID | Observable | Metric (from) | Comparator | Direction | Default bands | Feeds |
|----|-----------|---------------|------------|-----------|---------------|-------|
| **SAT-01** | Supernatant pond | pond area as % of facility footprint (NDWI, largest water body) | vs_design | high_bad | green <15 · amber 15–25 · red >25 | RA-01 overtopping |
| **SAT-02** | Beach width (buffer) | exposed beach as % of facility (NDWI + NDVI) | vs_baseline | low_bad | green ≥40 · amber 25–40 · red <25 | RA-01 / RA-02 |
| **SAT-03** | Vegetation encroachment | vegetated tailings as % of surface (NDVI) | vs_baseline | high_bad | green <15 · amber 15–30 · red >30 | monitoring |
| **SAT-04** | Embankment moisture | % of outer slope wet, NDMI>0 (B08,B11) | absolute | high_bad | green <10 · amber 10–25 · red >25 | RA-03 / RA-04 |

The set is extensible — add SAT-05 (freeboard / water-to-crest), SAT-06 (pond-to-wall distance,
the Merriespruit metric), SAT-07 (surface change vs previous scene) as the analytics mature. Each is
one more row; the engine does not change.

## 4. The grading algorithm (deterministic)

```
grade(value, bands, direction):
    if direction == 'low_bad':                  # bigger is safer (e.g. beach)
        band = green if value >= bands.green
             else amber if value >= bands.amber
             else red
    else:                                        # high_bad — bigger is worse (e.g. pond, moisture)
        band = green if value <  bands.green
             else amber if value <  bands.amber
             else red
    return band                                  # → severity: green=OK, amber=watch, red=action
```

The band maps 1:1 to the traffic light and to the escalation path (green logged, amber cross-checked,
red to the named owner) — identical to the TARP standard (M09), so satellite and instrument bands
speak the same language.

## 5. Comparators (the MAC scale)

Every rule declares how its threshold is set — the same three bases used across the platform:

- **absolute** — a fixed physical trigger (e.g. any moisture on the wall). Site-derived once.
- **vs_design** — against the design basis (e.g. pond vs the maximum operating pond). Reads from M01.
- **vs_baseline** — against this facility's own history/trend (e.g. beach shrinking, vegetation change).
  Requires a rolling baseline per facility.

## 6. Statements are templates, never free text

The plain-language output is a **template per (observable, band)** with placeholder substitution
(`{v}`, `{green}`, `{amber}`). Two parts: a `head` (the headline) and a `body` (the explanation).
Identical inputs produce identical words — that is what makes the write-up auditable and safe to put
in a report or a public disclosure.

```
SAT-01 · amber:
  head: "The pond is the number to watch"
  body: "~160 ha of water — {v}% of the facility, above the {green}% normal level. The danger is a
         pond that grows or drifts toward the wall and eats the freeboard (the Merriespruit
         mechanism). Verify freeboard at the gauge board and check the drawdown protocol."
```

No sentence is written by hand at run time. Editing a template is a versioned change (a ledger event).

## 7. Scalability model — one engine, many dams

- **Thresholds are configuration, not code.** They live in the `satellite_rule` rows keyed by
  `facility_id`. A global `facility_id = default` row seeds a new facility; the operator then
  calibrates. Adding a facility adds rows, not logic.
- **Analytics are gated by facility type** (`applies_when`). A dry-stack facility has no pond, so
  SAT-01/02 do not apply; a low-consequence facility may not warrant Sentinel-2 pond monitoring at
  all (ADR 0002). The engine skips rules whose gate is false.
- **Calibration is audit evidence.** The NDWI/NIR thresholds and the derivation (transect, validation
  imagery, residual error) are recorded, not silently set (ADR 0005). Changing a threshold is a
  ledger event with actor + justification.
- **Versioned.** A ruleset carries a version (v1.0). A facility pins a version; upgrading is deliberate
  and logged, so old results remain reproducible against the rules that produced them.

## 8. Scene fitness & publication gate

A rule only runs on a scene that passes the gate — **a wrong number is worse than none**:

- **Cloud** over the AOI below the configured fraction, else the scene is **withheld** (a reportable
  gap, logged — not silence).
- **Fitness** — expected error below a set fraction of the typical value (e.g. exposed sandbanks at
  low pond level inflate pond error); above it, the result is **refused**, not published (ADR 0005).
- Every published value carries an **uncertainty band** and the **scene id + calibration ref**. A
  single number without provenance is not a valid output.

## 9. Data-model additions

```
-- the rules (per facility, versioned)
satellite_rule            -- as §2; unique (facility_id, id, version)
ruleset_version           -- version, published_at, notes, ledger_event_id

-- the graded outputs (one row per rule per published scene)
satellite_result
  id, facility_id, scene_id, rule_id, ruleset_version
  value, unit, band, severity
  statement_head, statement_body        -- rendered from the template at publish time
  uncertainty, computed_at, ledger_event_id
```

`satellite_result` is a projection over the scene + the rule; it is reproducible and every row is a
ledger event (`analytic_published`).

## 10. Worked example — Parabola TSF1 · 3 Jul 2026

| Rule | Value | Band | Result (head) |
|------|-------|------|---------------|
| SAT-01 pond | 16.3% | **amber** | "The pond is the number to watch" |
| SAT-02 beach | 62.6% | green | "A wide beach is protecting the wall" |
| SAT-03 vegetation | 8.8% | green | "Vegetation encroachment is normal" |
| SAT-04 moisture | 5.7% | green | "The wall looks dry — the best news" |

Note SAT-01 lands **amber** purely from the rule (16.3% is between the 15% and 25% thresholds) — not
from a human deciding it "feels" like a watch item. That is the whole point: the grade is the rule's,
not the writer's. Change the thresholds for a different facility and the same value may grade
differently — correctly, and on the record.

---

*See also: ADR 0002 (registry-first, analytics gated by type), ADR 0005 (site-specific calibration as
audit evidence), the TARP standard (M09) for the shared band/escalation model, and data-model.md for
the registry and ledger.*
