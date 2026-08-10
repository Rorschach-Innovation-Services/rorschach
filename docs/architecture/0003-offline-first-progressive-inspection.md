# ADR 0003 — Offline-first PWA with progressive inspection forms

**Status:** Partially superseded by [ADR 0006](0006-whatsapp-bot-field-capture.md) — the *field
client* is now a WhatsApp bot, not a PWA. The **progressive, "all normal" capture thinking** below
still governs the bot's conversation design; the custom app is shelved for Phase 1.

## Context

The inspection app (M03b) is the pilot's centre of gravity — it is what makes the operator open the
system daily, and "faster than paper" is the adoption mechanism. Two facts constrain its design
hard: a tailings facility has **no reliable network coverage**, and the canonical inspection
taxonomy has **seven categories and ~40 parameters**. Presenting 40 fields guarantees the pilot
fails; requiring a signal guarantees it fails differently.

## Decision

Build the field client as an **offline-first PWA** (service worker + IndexedDB, full round
completable with no signal, sync on reconnect) with **progressive forms**: each category renders as
a single **"all normal"** control by default; flagging a category expands only *its* parameters. A
clean inspection is a handful of taps; only anomalies cost time.

## Why

- **Speed causes adherence, and adherence is the pilot metric.** Design for speed, measure
  adherence ("3 of 4 inspections completed this week" is the SHEQ manager's native language).
- **Camera-first.** Photo capture in ≤2 taps from any parameter, GPS + timestamp auto-attached.
  Colour-based parameters (seepage colour, water clarity, vegetation) are captured as *structured
  photo + ordinal scale*, with the previous inspection's photo shown alongside — free text does not
  trend.
- **The monitoring system is itself an inspected object.** Two of the seven categories are about
  instrumentation and alarms, from the standards — not optional.

## Consequences

- **No silent edits.** Post-submission changes create an amendment record referencing the original;
  they never overwrite (this pairs with [ADR 0004](0004-append-only-evidence-ledger.md)).
- Native-only gaps (background sync, reliable push) are out of Phase-1 scope; architect for a
  **Capacitor wrap in Phase 2** reusing the same codebase — one developer cannot ship a PWA plus
  two native clients in three months.
- Route guidance (ordered points from M01 geometry) gives the round a defined end so nothing is
  missed.

## Alternatives considered

- **Exhaustive single-page form:** matches the taxonomy literally, guarantees non-adoption.
- **Online-only web form:** simplest to build, useless at a facility with no coverage.
- **Native app first:** better background sync/push, but unaffordable at one developer in the
  timebox; deferred to Phase 2.
