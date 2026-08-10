# ADR 0002 — Registry-first facility twin, analytics gated by facility type

**Status:** Accepted

## Context

Every module resolves against a description of the facility: its geometry, its design criteria, its
consequence class, its accountable roles, its inspection plan. If that description is thin or wrong,
everything downstream is thin or wrong. The spec is also explicit that facility *type* changes what
can even be monitored: a filtered / dry-stack facility has no supernatant pond, so the M05 water
analytic is largely inapplicable and risk shifts to slope movement and seepage.

## Decision

Build **M01 (Asset Registry & Facility Digital Twin) first**, as a well-modelled schema seeded from
the site documentation, and have it **gate which analytics are offered** — by *storage method* and
by *consequence class*, not only by what data exists.

## Why

- **Freeboard is a stack, not a number.** M01 holds every allowance separately (minimum decant, wet
  season, extreme, contingency, flood spill, max operational level, operational freeboard) *with a
  lower bound as well as an upper one* — over-drainage is a reportable deviation.
- **A monument that moved must be distinguishable from ground that moved.** Instrument *position
  history* is a first-class record; relocation is a ledger event (see
  [ADR 0004](0004-append-only-evidence-ledger.md)).
- **Consequence class drives service tier.** Sentinel-2 pond monitoring only suits large,
  high-consequence facilities; classification decides whether M05 is even shown.

## Consequences

- **Seeding is data archaeology, not coding.** Budget real time with the in-house engineer; facility
  type must be *confirmed* before M05 is built (spec §9, blocking).
- Analytic availability is a computed property of the registry, so the UI must handle "not
  applicable to this facility" as a first-class state, not an error.

## Alternatives considered

- **Analytics-first, registry as config:** inverts the dependency; you cannot design a monitoring
  plan without first knowing which failure modes matter (spec puts M17's RPN baseline in Phase 1
  for exactly this reason).
- **Single `freeboard` number:** loses the lower bound and the per-allowance deviation detection —
  rejected as under-modelled against the design basis.
