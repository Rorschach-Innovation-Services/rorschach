# ADR 0001 — Python/FastAPI + PostgreSQL/PostGIS backend

**Status:** Accepted (mandated by build spec §7)

## Context

The Smart Club Platform — the process this project mirrors — runs on Vite/React + SST/DynamoDB in
`af-south-1`. It would be tempting to reuse that stack wholesale. But a TSF platform is a
*geospatial, raster-processing, time-series* system: it ingests Sentinel-2 scenes, computes NDWI
and NIR per pixel inside a crest-line polygon, delineates ponds, and cross-validates against
piezometer and GNSS time series. That is Python's and PostGIS's home ground, not DynamoDB's.

## Decision

Build the backend in **Python / FastAPI** on **PostgreSQL + PostGIS**, with S3-compatible object
storage and Celery/APScheduler for scheduled scene acquisition and threshold recomputation. The
field client is a **PWA**. All managed services; self-host nothing avoidable.

## Why

- **Geospatial is the core, not a bolt-on.** PostGIS gives native geometry (crest polygon, toe,
  calculation boundary, beach extent) and spatial queries; `rasterio`/`GDAL`/`xarray`/Earth Engine
  are the standard Python raster stack. Modelling M05 on DynamoDB would mean fighting the database.
- **One database, three jobs.** Geometry, instrument time series, and the append-only event store
  all live in one managed PostgreSQL — no second data engine to operate.
- **The domain is relational.** Facility ↔ instruments ↔ inspections ↔ analytic results ↔ ledger
  events have real joins and referential integrity that a single-table NoSQL design would obscure.

## Consequences

- Two different stacks across the RIS portfolio (this and Smart Club). Accepted: the *process* is
  shared (clickable prototype → docs/ADRs → phased hardening), the runtime is not.
- More geospatial ops surface (Earth Engine credentials, scene scheduling) than a pure CRUD app —
  inherent to the problem, priced into weeks 9–11.

## Alternatives considered

- **Reuse Node + DynamoDB (Smart Club stack):** no native geometry, poor fit for raster/time-series
  cross-validation; would need a bolt-on geospatial service anyway.
- **Supabase (Postgres + PostGIS available):** viable for the relational + geometry parts, but the
  raster/EO pipeline and scheduled recompute still want a Python worker tier — so FastAPI + managed
  Postgres is the lower-friction whole.
