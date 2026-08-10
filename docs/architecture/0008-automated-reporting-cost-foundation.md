# ADR 0008 — Compliance reports are generated from the ledger; cost telemetry from day one

**Status:** Accepted

## Context

The platform's purpose is compliance and auditing across four streams in one place, with
**automated reporting** — and a follow-on phase of operations research (OR) to reduce monitoring
and maintenance cost. The OR work is explicitly *not* part of the platform, but the platform must
produce the dataset it runs on; a baseline cannot be reconstructed after the fact (same logic as
the FMEA Detection baseline in spec §9).

## Decision

1. **Reports are projections over the evidence ledger, never hand-compiled.** A scheduled worker
   assembles each report (daily operator brief, weekly adherence, monthly technical, quarterly
   GISTM Topic VI disclosure pack, on-demand regulator evidence pack) directly from ledger events
   and stream outputs. Every generated report is itself a ledger event recording the chain head it
   was built from — a report is reproducible and tamper-evident by construction.
2. **GISTM conformance is a live scorecard**, mapped topic-by-topic to what the platform can
   evidence, alongside the standards register (SANS 10286/MHSA/DWS binding in SA; ICOLD B158/181,
   MAC, ANCOLD as adopted practice).
3. **Cost telemetry is captured per stream from day one:** unit cost, monthly cost, observation
   count and cost-per-observation for satellite, drone, IoT and manual rounds — per facility.

## Why

- Automated reporting is the operator's actual daily pain; "the audit trail writes the report" is
  the platform's version of GISTM Topic VI as a report template.
- The OR phase needs cost-vs-coverage data (which mix of streams covers each failure mode at what
  cost and cadence). Captured continuously it is free; reconstructed later it is impossible.

## Consequences

- Report templates are versioned artefacts; a change to a template is a ledger event.
- The cost model stays simple (unit × volume) until real invoices exist — precision follows use.
- The OR phase consumes an export (cost, coverage, adherence, maintenance history) — no OR logic
  is built into the platform itself.
