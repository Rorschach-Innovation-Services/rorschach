# Tailings Dam Platform

A monitoring, surveillance and auditing platform for **Tailings Storage Facilities (TSFs)**.
The daily user is a mine's **Environmental / SHEQ manager** — *not* a geotechnical engineer —
so the product's job is to make the **inspection happen on schedule**, capture defensible
evidence, and escalate anything beyond their competence to the Engineer of Record in one tap.

The full reasoning and source provenance live in
[`docs/spec/TSF-Monitoring-Platform-Module-Architecture-v0.4.md`](docs/spec/TSF-Monitoring-Platform-Module-Architecture-v0.4.md).
The Phase-1 pilot scope lives in
[`docs/spec/TSF-Platform-Phase1-Build-Spec-v1.0.md`](docs/spec/TSF-Platform-Phase1-Build-Spec-v1.0.md).

> **Phase-1 pilot ships four things and nothing else:** the asset registry (M01), the
> inspection PWA (M03b), one satellite analytic (M05 water/beach/freeboard), and the append-only
> evidence ledger beneath both (M14). If anything is at risk, the satellite analytic is cut
> first — the inspection habit is what converts the pilot.

## Where this is right now

This is the **clickable prototype** stage — self-contained HTML with no build step, exactly how the
Smart Club Platform started. Two surfaces, one platform:

| File | Surface | For |
| --- | --- | --- |
| [`prototype/console.html`](prototype/console.html) | **Management console** (desktop) | The oversight side — dashboard, inspection review, M05 analytics, registry, evidence ledger, alerts, risk baseline, **and onboarding** (facility + users/inspectors). Built **first**. |
| [`prototype/index.html`](prototype/index.html) | Field app (mobile) — *reference only* | Superseded as the Phase-1 field client by the **WhatsApp bot** ([ADR 0006](docs/architecture/0006-whatsapp-bot-field-capture.md)). Kept for its progressive-capture conversation logic and as a possible Phase-2 option. |

Open either directly in a browser (double-click), or serve the folder:

```bash
cd "Tailings Dam Platform/prototype" && python3 -m http.server 4173
```

Then browse to <http://localhost:4173/console.html> or <http://localhost:4173/index.html>.

The console has a **role switcher** (bottom-left) — SHEQ manager / Engineer of Record / Responsible
Executive / read-only auditor — previewing the role-based access that ships from day one.

## Intended stack (Phase 1)

Mandated by the build spec §7 — deliberately **different** from the Smart Club stack, because a
TSF platform is geospatial and Python-native. See
[ADR 0001](docs/architecture/0001-python-postgis-stack.md).

| Layer | Choice | Note |
| --- | --- | --- |
| API | Python · FastAPI | |
| Database | PostgreSQL + **PostGIS** | Geometry, time series, and the event store in one managed DB |
| Geospatial | rasterio · GDAL · xarray · Earth Engine Python API | Sentinel-2 pipeline for M05 |
| Object storage | S3-compatible | Imagery + inspection photos |
| Scheduling | Celery / APScheduler | Scene acquisition, threshold recomputation |
| Field capture | **WhatsApp bot** — WhatsApp Business Cloud API / Twilio → FastAPI webhook | Inspectors send photos for alerts and reply to scheduled prompts; no app to install. See [ADR 0006](docs/architecture/0006-whatsapp-bot-field-capture.md) |
| Auth | Managed provider | Role-based from day one |

## How it works

- **Registry-first (M01).** One authoritative facility record — geometry (PostGIS), construction
  method, storage method, consequence class, the freeboard *stack*, instrument position history,
  accountable roles, and the inspection plan. Everything else resolves against it. See
  [ADR 0002](docs/architecture/0002-registry-first-facility-twin.md).
- **WhatsApp field capture (M03b).** Inspectors work entirely in WhatsApp — send a photo to raise an
  alert, or reply to a scheduled prompt to complete a round ("*all normal*" or a photo). No app to
  install; identity is their WhatsApp number. WhatsApp's own store-and-forward handles bad coverage.
  The progressive "all normal" thinking from [ADR 0003](docs/architecture/0003-offline-first-progressive-inspection.md)
  survives as the bot's conversation design; the channel decision is
  [ADR 0006](docs/architecture/0006-whatsapp-bot-field-capture.md).
- **Append-only evidence ledger (M14).** Hash-chained, immutable event store — no UPDATE, no
  DELETE. Every observation, alert, acknowledgement, dismissal, threshold change and role
  reassignment is an event. It cannot be retrofitted, so it exists from the first commit. See
  [ADR 0004](docs/architecture/0004-append-only-evidence-ledger.md).
- **Site-calibrated analytic (M05).** No universal NDWI/NIR thresholds exist — every site carries
  its own calibrated, audit-recorded derivation. See
  [ADR 0005](docs/architecture/0005-site-specific-threshold-calibration.md).
- **SHEQ-manager framing.** No raw geotechnical output on a primary screen; every analytic surfaces
  as a plain-language status with a colour state, the number one tap away. Acknowledging an alert
  is **never** a determination of safety.

## Project layout

| Path | Purpose |
| --- | --- |
| `prototype/` | Self-contained clickable prototype (`index.html`), no build step |
| `docs/spec/` | The two source specs — architecture (the why) and Phase-1 build (the what) |
| `docs/architecture/` | [overview](docs/architecture/overview.md), ADRs, [data model](docs/architecture/data-model.md) |
| `docs/guides/` | Build guides (added as Phase-1 modules land) |
| `docs/api/` | API reference (added when FastAPI work begins) |

## Pre-build blockers (spec §9)

Two items block the satellite analytic and must be resolved before §5 is built:

1. **Confirm facility type.** Upstream/centreline slurry → M05 as written. Filtered / dry stack →
   there is no supernatant pond and M05 is largely inapplicable; Phase 1 re-cuts around slope
   movement and seepage. *This is the one genuinely blocking unknown.*
2. **Resolve the base-rate discrepancy** (Azam & Li 2010: 1.2% annual vs 1.2% cumulative over
   ~100 years) before any QRA/ROI figure goes to the operator.

## Suggested sequencing (spec §10)

| Weeks | Focus |
| --- | --- |
| 1–2 | Schema, PostGIS geometry, event store. Seed M01 from site docs. Capture baselines. |
| 3–6 | Inspection PWA — offline capture, progressive forms, photo, sync. **The core deliverable.** |
| 7–8 | Schedule adherence, overdue logic, escalation. Instrument data ingestion. |
| 9–11 | Sentinel-2 pipeline. Threshold calibration. Cross-validation view. |
| 12 | Hardening, operator training, pilot review pack. |

**Week 6 checkpoint:** if the PWA is not in the operator's hands and in daily use, cut M05 to pond
area only and protect the inspection app.

_Modelled on the Smart Club Platform process. Powered by RIS._
