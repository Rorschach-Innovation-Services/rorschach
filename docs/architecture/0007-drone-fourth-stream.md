# ADR 0007 — Drones (UAV photogrammetry) as the fourth inspection stream

**Status:** Accepted

## Context

The fusion model (P1) had three streams: satellite (M05), IoT telemetry (M03a) and manual rounds
(M03b). The research corpus points at a resolution gap between them: Sentinel-2 is 10 m and
~5-daily; the ground instruments are point measurements; a person cannot safely walk the deposit.
UAV photogrammetry sits exactly in that gap — Rauhala et al. (2017) demonstrate UAV survey design
for TSFs, Slingerland et al. (2018) erosion identification, and Jeong & Kim (2020) convert a
UAV-derived **beach distance into an FoS-linked safety criterion**, which is the bridge from
measurement to a defensible criterion (M05 → M17).

## Decision

Add **drone/UAV photogrammetry as a fourth, first-class inspection stream**: scheduled flights
producing orthomosaics + DEMs (cm-level), used for (a) beach distance vs the critical-distance
FoS criterion, (b) rill/erosion detection, (c) crest and wall visual survey — plus ad-hoc flights
as job-card follow-ups. Products land in S3 with GPS ground control; every flight is a ledger
event; findings raise alerts/job cards like any other stream.

## Why

- **Safety first:** manual beach measurement is discouraged where it requires walking the deposit
  ([Z24]) — remote measurement is a safety control, independent of cost.
- **Cross-validation (M08):** the DEM validates the satellite beach/pond edge, strengthening the
  pilot's proof artefact.
- **Coverage:** slope erosion and cracking are visible at cm resolution long before 10 m pixels.

## Consequences

- The coverage matrix (failure mode × source) gains a Drone column; drone cost per flight enters
  the cost telemetry (ADR 0008).
- Flight ops (pilot licensing, weather windows) are an operational dependency priced into the
  monitoring plan, not assumed free.
- Deep-learning screening of imagery (SSD/YOLO pipelines per Li et al. 2020, Yu 2026) is a
  Phase-2+ triage option and is governed by M18 — no model alerts without shadow-mode validation.
