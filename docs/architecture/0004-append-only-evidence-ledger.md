# ADR 0004 — Append-only, hash-chained evidence ledger from the first commit

**Status:** Accepted

## Context

The dominant failure mode for well-monitored dams is *governance*, not instrumentation: risks not
communicated to people with authority to act, concerns overridden by financial pressure, triggers
silently relaxed. The platform's commercial differentiator (M14) is that it makes monitoring
*defensible* — to a board, an Engineer of Record, an insurer, a lender, a regulator, a court. That
property cannot be bolted on later: an audit trail you started keeping halfway through proves
nothing about the half before.

## Decision

Ship **M14's ledger primitives in Phase 1**, from the first commit. An **append-only event store**:
every consequential action is an immutable, **hash-chained** record with actor, timestamp and
payload. **No UPDATE or DELETE** on the event table. All mutable state ("is this overdue", "current
threshold", "pond area today") is derived as a **projection** over the log. Corrections are new
events referencing prior ones.

## Why

- **Retrofitting immutability is impossible.** The value is the unbroken chain; a gap is the whole
  ballgame.
- **Silently relaxing a trigger is the exact governance failure the platform exists to catch.**
  Threshold changes — including *who* and *why* — are events. So are alert dismissals with
  justification, instrument relocations, design-criterion changes and role reassignments.
- **For this operator the ledger is a defence, not surveillance.** Present it that way in the UI and
  in the contract: demonstrable proof that monitoring occurred and action followed.

## Events (Phase 1)

Inspection submitted / amended · photo captured · analytic result published · threshold created or
**changed** (who + why) · alert raised / acknowledged / **dismissed with justification** ·
instrument relocated · facility design criterion modified · role reassigned.

## Consequences

- Reads are served from projections, not the raw log — the log is the source of truth, projections
  are rebuildable caches.
- "Delete" is a product-level concept expressed as a compensating event, never a row deletion.
- Pairs with [ADR 0003](0003-offline-first-progressive-inspection.md): offline events carry their
  client timestamp and are chained on sync, never overwritten.

## Alternatives considered

- **Standard mutable CRUD tables + an audit-log side table:** the audit log becomes advisory and
  drifts from the real state; exactly the "trust us" posture the product is selling against.
- **Blockchain / external notarisation:** over-engineered for a single-tenant pilot; a hash chain in
  Postgres with controlled write access meets the tamper-evidence bar without the operational cost.
