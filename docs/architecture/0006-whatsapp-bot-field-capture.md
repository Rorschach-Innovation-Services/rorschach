# ADR 0006 — WhatsApp bot for field capture (supersedes the field-app half of ADR 0003)

**Status:** Accepted · **Supersedes:** the field-client decision in [ADR 0003](0003-offline-first-progressive-inspection.md)

## Context

ADR 0003 assumed the in-field surface was a custom offline-first **PWA** the inspector installs
and opens. In practice the inspectors are operators and contractors who already live in WhatsApp
all day, on their own phones, and will not reliably install, update or open another app — the exact
adoption risk the spec warns about. Asking them to learn a bespoke app fights the "faster than
paper" adoption mechanism instead of serving it.

The management side is unaffected: SHEQ managers, the Engineer of Record and the Responsible
Executive continue to work in the **desktop management console**.

## Decision

**Field capture is a WhatsApp bot, not an app.** Inspectors interact entirely through WhatsApp:

- **Alert photos (primary).** An inspector sends a photo (with an optional caption) to the bot at
  any time. The bot tags it with the sender, timestamp and — where the phone shares it — location,
  classifies it against the inspection taxonomy, raises an **alert** and notifies the SHEQ manager.
- **Scheduled rounds.** On the inspection plan's cadence the bot messages the assigned inspector to
  prompt the round, and walks them through the categories as a short guided chat ("Reply *all normal*
  or send a photo"). A clean round is a couple of replies; only anomalies cost time — the same
  progressive principle as ADR 0003, expressed as a conversation.
- **No login, no install.** Identity is the inspector's WhatsApp number, captured at onboarding.

Mechanism: the **WhatsApp Business Platform (Cloud API)** or **Twilio for WhatsApp** delivers
inbound messages to a FastAPI **webhook**; media (photos) are pulled and written to S3; each message
becomes one or more ledger events (M14) and, where relevant, an inspection record (M03b) or alert
(M12). Outbound prompts and confirmations use pre-approved message templates.

## Why

- **Zero adoption friction.** The inspector uses the app they already have. Nothing to install,
  update, or be locked out of.
- **Offline is the network's problem, handled.** WhatsApp already queues and retries on bad
  coverage — we inherit store-and-forward instead of building IndexedDB sync ourselves.
- **Camera-first is native.** Sending a photo is the single most practised action on the device;
  GPS/time metadata rides along.
- **It still honours the non-negotiables.** Structured capture (the bot asks the ordinal/colour
  questions), immutable events (every inbound message is a ledger event), and "acknowledging ≠
  clearing" (the bot never tells an inspector a condition is safe) all carry over.

## Consequences

- **Parsing replaces form validation.** The bot must interpret free-form replies and photos into the
  structured taxonomy, with a fallback to explicit multiple-choice prompts when confidence is low.
  Ambiguous cases route to the console for a human.
- **Template + opt-in constraints.** WhatsApp requires pre-approved templates for business-initiated
  messages and an opt-in — captured at inspector onboarding.
- **Media handling.** Photos are fetched from the provider and stored to S3 with the originating
  message id retained for chain-of-custody.
- **The PWA is not built for Phase 1.** [ADR 0003](0003-offline-first-progressive-inspection.md)'s
  progressive-capture *thinking* still governs the bot's conversation design; the *app* is shelved.
  The existing `prototype/index.html` is kept as a reference for that conversation logic and a
  possible Phase-2 native/PWA option, not a Phase-1 deliverable.

## Alternatives considered

- **Custom offline PWA (ADR 0003):** best structured capture, worst adoption for this user group;
  an app nobody opens captures nothing.
- **SMS bot:** universal reach, but no reliable media (photos are the point) and no rich prompts.
- **Telegram/Signal bot:** better APIs, but WhatsApp is where these users already are in this market.
