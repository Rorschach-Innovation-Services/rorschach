# ADR 0005 — Site-specific NDWI/NIR calibration as audit evidence

**Status:** Accepted

## Context

M05 quantifies the supernatant pond from Sentinel-2 (NDWI and NIR reflectance inside M01's
calculation boundary). There are **no universally applicable NDWI or NIR thresholds** — they depend
on the facility, the season, the tailings mineralogy and the beach geometry. A published South
African upstream platinum reference (Impala Dam 4) adopted NDWI 0.11 and NIR 870, but those are a
*starting point for a comparable facility*, not a config default to copy.

## Decision

Every site carries its **own calibrated threshold set with a documented derivation**, and that
derivation is stored as **audit evidence, not a config value**: transect definition, monthly
profiles across a full year, conservative upper/lower bounds, independent very-high-resolution
validation imagery, the area-mismatch minimisation, the adopted thresholds, the validation dates and
the residual error. A threshold change is a ledger event ([ADR 0004](0004-append-only-evidence-ledger.md)).

## Why

- **Provenance has to hold under interrogation.** If a regulator, insurer or expert panel asks why a
  threshold was set where it was set, the answer is a recorded derivation, not "we used the value
  from a paper."
- **Uncertainty is first-class.** Every figure carries its band; pond-area error (~±20 000 m² in the
  reference) propagates into derived beach and freeboard figures. The module **refuses to publish**
  where expected error exceeds a configured fraction of typical pond area, and states the achieved
  band on every output.
- **Cross-validation is the differentiator.** Raw sensor access lets us publish satellite-derived
  pond metrics *alongside* piezometer, GNSS and decant-level readings on a shared timeline — the
  thing the reference implementation in this field never managed. This is the pilot's proof artefact.

## Consequences

- Calibration is a **mandatory per-site sub-process** (weeks 9–11), not a settings screen. It needs
  independent validation imagery, which has cost and lead time.
- Blocked by spec §9 item 1: if the facility is filtered / dry-stack there is no pond and this ADR
  largely does not apply — Phase 1 re-cuts M05 around slope movement and seepage.
- Known edge cases are flagged, not hidden: low pond level with exposed sandbanks; pond edges against
  a gently sloping beach are inherently fuzzier than against a training wall.

## Alternatives considered

- **Global default thresholds:** simplest, and wrong everywhere — violates design principle P4 and
  produces indefensible numbers.
- **Ship the Impala reference values as defaults:** acceptable only as a labelled starting point for
  a *confirmed-comparable* facility; never as the published operating threshold without site
  calibration.
