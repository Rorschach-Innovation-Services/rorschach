# Tailings Dam Monitoring, Surveillance & Auditing Platform
## Module Architecture — v0.4

**Status:** Working draft. Derived from literature set of 6 sources. To be extended as further sources are reviewed.
**Changes from v0.3:** added §0.5 Standards Register; GISTM six-topic structure mapped into M14; definitive visual inspection checklist into M03b; freeboard decomposition closed out in M05; beach criteria quantified; MAC displacement risk scale into M09; base-rate discrepancy flagged for resolution; Church of England disclosure figures added; new acquisition leads.

### Source anchors

| Ref | Citation | Grade | Contribution |
|---|---|---|---|
| **[T19]** | Thomas et al. (2019), *EO data and satellite InSAR for the remote monitoring of TSFs: a case study of Cadia Mine*, Paste 2019, ACG | **A** | InSAR processing chain, precursor deformation, inverse velocity forecasting, quantified uncertainty, monitoring-method taxonomy |
| **[O22]** | O'Donovan, Adam & Torres-Cruz (2022), *Remote sensing of the decant pond of tailings dams*, JSAIMM 122(4):167–172 | **A** | Pond area measurement, NDWI/NIR site-specific threshold calibration, validation protocol, accuracy envelope |
| **[Z24]** | Zare, Nasategay, Gomez, Moayedi Far & Sattarvand (2024), *A Review of Tailings Dam Safety Monitoring Guidelines and Systems*, Minerals 14(6):551 | **B** | **The map of the standards landscape.** Comparative review of CDA, MAC, ICOLD, ANCOLD, SANS 10286, GISTM, UNECE, ISO, EU BAT; visual indicator taxonomy; catalogue of 17 monitoring technology studies. NIOSH-funded; SRK and Mackay School authorship. Its principal value is as a **guide to Grade S instruments**, not as a primary authority. |
| **[L19]** | Lumbroso et al. (2019), *The potential to reduce the risks posed by tailings dams using satellite-based information*, IJDRR 38:101209 | **B** | DAMSAT at proposal stage — service architecture, failure-mode/warning-sign mapping, EO capability table, service tiering, governance diagnosis |
| **[D21]** | HR Wallingford (2021), *The DAMSAT Project — Case Study*, BE-106 R02-00 | **B** | DAMSAT at completion — delivered module set, named consequence models, two-tier packaging, customer segmentation, lessons learnt. Self-reported outcomes. |
| **[A25]** | Abankwa et al. (2025), *Advancing Tailings Dam Safety*, Am. J. Mining & Metallurgy 8(1):1–12 | **C** | Framing only — risk-model vocabulary, GISTM as anchor, ML failure modes by counter-example |

### Source grading scheme

Because this document will itself become part of the basis-of-design record, every source carries a grade. If a regulator, insurer or expert panel interrogates why a threshold was set where it was set, the provenance has to hold.

- **Grade S — standards and guidelines.** Authoritative instruments. The question is not "is this reliable" but "is this the *applicable* instrument in this jurisdiction, and is this the *current* edition." Grade S sources set requirements rather than informing design. Every Grade S citation records edition, date, jurisdiction and mandatory-vs-advisory status. See §0.5.
- **Grade A** — peer-reviewed, method reproducible, uncertainty reported. May be sole justification for a design decision or a quoted figure.
- **Grade B** — peer-reviewed or authoritative practitioner source; directionally reliable. Framing and architecture may rest on it; numeric values require corroboration before external use.
- **Grade C** — weak or unverifiable provenance. Terminology, framing and hypothesis generation only. **Never the sole basis for a design decision, a threshold, or any figure presented to a client, regulator or investor.**

### Provenance note on ANCOLD — updated in v0.4

The ANCOLD guidelines are still **not sighted**, but [Z24] now provides an independent secondary description, which changes the position in two ways:

1. **Structure is corroborated.** ANCOLD (2019, revising 2012) defines five inspection levels — comprehensive, intermediate, routine, special, emergency — with routine frequency indexed to failure consequence category, and enumerates 18 visual inspection items. The 2019 revision added learnings on earthquake-induced static liquefaction. The freeboard decomposition is now usable (see M05). All of this is consistent with the earlier summary and can be treated as **Grade B pending sight of the primary text**.
2. **One claim is contradicted.** The earlier summary characterised ANCOLD as *more numerically specific than GISTM*. [Z24] states the opposite — that ANCOLD is less detailed than an ICOLD bulletin, and is best used as a concise foundation for building a site-specific guide, ideally alongside ICOLD for non-Australian sites. **The specific tables cited in that summary (inspection frequency, contingency freeboard, spillway AEP, factors of safety) remain unverified and must not be implemented.** If numerically specific criteria are the objective, [Z24] points at **ICOLD Bulletin 158** and **Bulletin 181** as the richer sources.

Jurisdiction position unchanged: for South Africa, SANS 10286 + MHSA + DWS classification is binding, GISTM is the international anchor, ANCOLD is persuasive practice.

---

## 0. Facility objectives

v0.1 and v0.2 stated what the *platform* must do but never stated what the *facility* is supposed to be achieving. That is the thing the platform monitors compliance against, so it belongs at the front.

**Containment objectives** *(structure attributed to [A12] — PENDING VERIFICATION against primary text)*
1. Safe and stable containment of tailings and contaminants
2. Safe management of decant water and rainfall runoff
3. Management of seepage
4. Capability to achieve effective long-term closure with no unacceptable environmental legacy
5. All of the above achieved cost-effectively

**Operational control objectives.** The summary of [A12] decomposes the above into eight operational controls: tailings distribution and geometry, deposition cycle, pond level and position, stormwater management, access control, water recycle, separation of clean and contaminated water, and dust. Each maps to analytics already specified in M04–M07 — which is a useful corroboration of P3, sourced from a standards body rather than inferred from case studies. **The eight-item list is PENDING VERIFICATION.**

**Why this matters architecturally.** P3 says every observable maps to a failure mode. §0 adds the converse: every facility objective must map to at least one monitored observable, or the platform is silent on something the facility is contractually and legally obliged to achieve. M14's adequacy test now runs in both directions.

---

## 0.5 Standards register

Landscape mapped from [Z24]. **None of these instruments has yet been read in the original.** This register is the acquisition plan and the M14 control-library skeleton — not a conformance mapping. Status column records what we know, not what we have verified.

| Instrument | Issuer / edition | Scope & relevance | Binds which modules |
|---|---|---|---|
| **GISTM** | ICMM + UNEP + PRI, Aug 2020 | **Primary international anchor.** Three layers: topics → principles → requirements, across six topics. Goal stated as zero harm with zero tolerance for fatality. Includes flood and earthquake design guidance and consequence classification matrix. | M01, M09, M11, M12, M13, **M14**, M15, M16, M17, M19 |
| **SANS 10286** | SABS, 1998 | **Binding in South Africa.** Originally the Code of Practice for Mine Residue Deposits, published in response to Merriespruit. Five principles: continual management; waste and impact minimisation; precautionary principle; internalisation of costs; full-lifecycle assessment including post-closure. | M01, M14 — SA market |
| **MHSA + DWS dam safety classification** | RSA statutory | Binding SA statutory layer. Not covered by [Z24]; acquisition required. | M14 |
| **CDA Dam Safety Guidelines** | Canadian Dam Association, 2007 (2013 ed.) | Reported by [Z24] as the **most-used guideline in the mining industry** per the Church of England survey. Defines a dam as retaining ≥30,000 m³. Provides a closed-loop dam safety management system and a four-purpose surveillance program definition. Nine routine visual parameters. | M03b, **M13**, **M14** |
| **MAC Tailings Guide** (3rd ed. v3.1) + **OMS Manual** (2nd ed.) | Mining Association of Canada, 2017 / 2019 | "Surveillance" defined as inspections + monitoring. Eleven monitoring parameters. Four-level displacement risk scale. Beach angle and width control guidance. Both freely downloadable. | M03b, M05, **M09** |
| **ICOLD Bulletin 158** — *Dam Surveillance Guide* | ICOLD, 2018 | **Most detailed surveillance instrument identified.** Four visual inspection categories; inspection extent tabulated by dam section (downstream face / crest / upstream face); weekly routine, daily after a major or extreme event; includes a section on monitoring automation. | **M03b**, M02, M09 |
| **ICOLD Bulletin 181** — *Tailings Dam Design: Technology Update* | ICOLD, 2019 | Global beach slope dataset; beach length shown to be the inverse of beach slope; overall beach slope 1–4% recommended for uniform distribution. | **M05** |
| **ICOLD Bulletin 118** — *Automated Dam Monitoring Systems* | ICOLD, 2000 | Automation guidance and case histories. Dated, but the only instrument specifically on automated monitoring. | M03a, M08 |
| **ICOLD Bulletins 74, 103, 104, 139** | ICOLD | Tailings-specific monitoring and surveillance. B104 is *Monitoring of Tailings Dams*. B106 links crest tension cracks to shear-failure instability. | M03b, M09 |
| **ANCOLD Guidelines on Tailings Dams** | ANCOLD, 2019 (rev. from 2012) | Five inspection levels; routine frequency indexed to consequence category; 18 visual inspection items; freeboard decomposition; upslope vs downslope discharge. 2019 revision adds static liquefaction. | M01, M03b, **M05**, M09, M14 |
| **UNECE Safety Guidelines & Good Practices for TMFs** | UNECE, 2014 | Two parts: recommendations to member states, authorities and operators; and technical/organisational guidance across the full lifecycle. | M14, M19 |
| **ISO 9000 family** | ISO, 2015–2018 | Defines **monitoring** as determining the status of a system, process, product, service or activity; **inspection** as conformity to design specification. That distinction should be adopted verbatim in our data model. | M03, M08, M14 |
| **ISO 14001:2015** | ISO | Requires monitoring methods to ensure results are **reliable, reproducible and traceable**, that timing is coordinated with the need for analysis, and that trend reporting is enabled. A standards-backed mandate for M08. | **M08**, M06 |
| **ISO 31000:2018** family | ISO | Risk management framework underpinning most other guidelines' risk programmes. Seven risk treatment options: avoid; take/increase to pursue opportunity; remove sources; change likelihood; change consequences; share; retain by informed decision. | **M17** |
| **EU BAT reference document** — waste from extractive industries | European Commission | Generic BATs (organisational & corporate management system; environmental management system, both adapted for closure) and risk-specific BATs (geotechnical investigation of supporting strata, dam material selection, geotechnical analysis of ponds/dams including seismic, and of heaps). | M14, M17 |
| **Queensland Dam Safety Management Guideline v3** | DRDMW, 2024 | Eight-component programme: investigation; design and construction documentation; SOPs; operation and maintenance; inspection and evaluation reports; dam safety review reports; emergency action plan; emergency event reports. Not tailings-specific. | M13, M14 |

### Two findings from the register worth acting on

**1. Every major instrument is criticised for the same omission.** [Z24]'s limitations section concludes that ICOLD does not specify visual inspection parameters in detail, the Queensland guideline is not tailings-specific, **SANS 10286 lacks clarity on incorporating advanced monitoring technologies and international best practice**, **GISTM lacks explicit recommendations for integrating emerging monitoring technologies**, and the EU BAT document needs the same. The gap the standards leave open is precisely *how to operationalise modern monitoring technology against a conformance obligation.* That is the product.

**2. The South African instrument is 27 years old.** SANS 10286 dates from 1998 and predates practical satellite EO entirely. For Niall's primary market this cuts both ways: there is no prescribed method to conflict with, but equally no clause to point at when selling. The commercial route in South Africa is therefore GISTM conformance and investor/lender pressure rather than statutory compliance — which reinforces the DFI/EPFI segment identified in M15. Confirm whether SANS 10286 has been revised since 1998 before relying on this.

---

### Appraisal note on [A25]

[A25] is graded C. The defects are specific and material, and are recorded here so the decision is auditable rather than a matter of taste:

1. **Fabricated or broken citations.** Ref [1], cited for the Brumadinho disaster, is a nucleoside chemistry abstract. Ref [2], cited for monitoring gaps, is a palaeontology paper on phylogenetic analyses. Refs [10] and [13] are identical and list "Brumadinho, P." as an author surname.
2. **Figure–caption mismatch.** Figure 1 is captioned "Frequency of Tailings Dam Failures" but presents four photographic frames of a collapse sequence. No frequency data appears.
3. **Internal contradiction.** The introduction asserts failure frequency has risen sharply, particularly in the last five years; the results section asserts it has decreased slightly over the past decade. Both cannot hold.
4. **ML accuracy figures are not interpretable.** Reported accuracies of 80–92% are given without dataset size, class balance, or train/test protocol. Against an annual probability of failure of order 1.2% [L19], a model that always predicts "no failure" scores ~98.8% accuracy. The reported figures are therefore consistent with models performing *worse than trivial*. This is the single most important reason the paper cannot inform model design — but it is a useful negative lesson, and is why M18 now exists.
5. **Undefined FTA output.** A 31.6% probability of failure is stated with no exposure period, no fault tree structure, and no basic-event probabilities.
6. **No survey methodology.** Expert interviews and a survey are claimed as primary data with no sample size, instrument, response rate or ethics statement.
7. **Unnormalised regional comparison.** Failure counts by region are compared without normalising by facility population. China alone holds an estimated 12,000+ storage facilities [L19]; raw counts cannot support the regulatory-strength conclusion drawn.
8. **Citation padding.** Several references (petroleum nanotechnology, transport infrastructure, green technology performance) are unrelated to the subject.

Publication context is consistent with the above: a five-week receipt-to-acceptance cycle in a journal from a publisher widely flagged as questionable, with an author group whose stated affiliations are in petroleum and general civil engineering rather than geotechnics or tailings.

**What is nonetheless usable:** FMEA, FTA and QRA are legitimate standard methods regardless of how this paper applies them, and they were a genuine gap in v0.1. GISTM is correctly identified as the governing international standard. And the reference list, while contaminated, contains several credible leads worth pulling directly (see §5).

---

## 1. Design principles

These are the non-negotiables the literature forces on us. Every module is judged against them.

**P1 — Fusion, not substitution.** No single technology is sufficient. [T19] frames a robust monitoring plan as a combination of manual inspection, remote sensing, CCTV, and instrumentation. [L19] positions EO as complementary to in-situ surveillance. The platform is a fusion layer, not a satellite product.
*Caveat added in v0.3:* fusion presumes access, and access is not guaranteed. DAMSAT was unable to complete its planned cross-comparison of satellite, in-situ and operator manual measurements because permission to install instruments on active mine sites could not be obtained [D21]. The blocker was commercial and legal, not technical. Architecture must degrade gracefully when only the satellite leg is available — which is also, not coincidentally, the regulator and NGO use case.

**P2 — Governance failure is the dominant failure mode.** [L19] identifies why well-monitored dams still fail: risks not communicated to people with authority to act; concerns overridden by financial pressure; monitoring not aligned to failure modes; poor equipment or staff capacity; no specialist interpretation or independent review; regulators without enforcement capacity. Six of six are organisational. **This makes the audit and escalation modules the core product, not an add-on.**

**P3 — Every observable maps to a failure mode.** Monitoring that is not focused on potential failure modes is explicitly called out as a cause of missed failures [L19]. Nothing enters the system as a free-floating metric.

**P4 — Site-specific calibration is mandatory.** There are no universally applicable NDWI or NIR water-detection thresholds [O22]; InSAR coherence varies seasonally with local vegetation [T19]. Every site carries its own calibrated parameter set with a documented derivation.

**P5 — Uncertainty is a first-class output.** [T19] reports 3.3 mm at 95% confidence derived from stable-area statistics; [O22] reports pond area errors around ±20 000 m², which sets a minimum viable facility size. Every measurement is emitted with an uncertainty band and a fitness-for-purpose flag.

**P6 — Tiered cost, tiered service.** [L19] separates Level 1 (free public data: Sentinel-1, Sentinel-2, GFS) from Level 2 (commercial: high-resolution SAR, sub-metre optical, ECMWF, in-situ GNSS). Commercial architecture must mirror this.

**P7 — Multi-stakeholder by design.** The transparency argument in [L19] only works if regulators, NGOs, insurers, investors and affected communities can hold differentiated views of the same evidence base.
*Caveat added in v0.3:* DAMSAT found it difficult to engage public and private institutions in the same programme because of data transparency concerns [D21]. Differentiated access control is necessary but not sufficient — the real obstacle is contractual and reputational, and it must be solved commercially before it is solved technically. See M15.

**P8 — Adoption is a design constraint, not a deployment phase.** DAMSAT delivered its technology successfully — five system versions, 31 sites, all KPIs met — yet could not secure final agreements for any stakeholder to take ownership beyond the project, and reported that confining testing to pilot sites made trust harder to build [D21]. Government staff turnover repeatedly reset the buy-in process. A monitoring platform that nobody owns after the funding ends has not reduced risk. Capacity building, institutional continuity and handover are therefore first-class modules (M19), not project overhead.

---

## 2. Module map

```
LAYER 5  DELIVERY        M15 Stakeholder Portal & Access Tiers
                         M16 Visualisation, Reporting & API
                         M19 Adoption, Capacity Building & Programme Sustainability

LAYER 4  GOVERNANCE      M12 Alerting, Escalation & Accountability
                         M13 Action & CAPA Workflow
                         M14 Audit, Assurance & Conformance   ← core differentiator

LAYER 3  INTERPRETATION  M09 Failure Mode & Warning Sign Engine
                         M10 Predictive & Risk Models
                         M11 Consequence & Impact Modelling
                         M17 Structured Risk Assessment (FMEA / FTA / QRA / Bowtie)
                         M18 Model Governance & ML Assurance  (cross-cutting)

LAYER 2  ANALYTICS       M04 InSAR Deformation Processing
                         M05 Water, Beach & Freeboard Analytics
                         M06 Chemical Stability & Vegetation Analytics
                         M07 Change Detection & Site Activity
                         M08 Data Quality & Uncertainty Engine  (cross-cutting)

LAYER 1  ACQUISITION     M02 EO Ingestion (SAR, optical, hydromet, seismic)
                         M03 In-Situ Ingestion (IoT telemetry + manual inspection)

LAYER 0  FOUNDATION      M01 Asset Registry & Facility Digital Twin
```

---

## 3. Module specifications

### M01 — Asset Registry & Facility Digital Twin

**Purpose.** The single authoritative description of every facility. Everything else resolves against it.

**Holds:**
- Facility identity, operator, ownership chain, lifecycle phase (construction / operation / care & maintenance / decommissioning / closure / orphaned). [L19] stresses that monitoring requirements change by phase and that abandoned and orphaned facilities are the largest unmonitored population.
- Construction method — upstream, centreline, downstream [T19]. Upstream is flagged as most vulnerable, and active facilities as more failure-prone than inactive ones (Rico et al. 2008, via [T19]).
- **Storage method** — conventional slurry impoundment, thickened, paste, filtered/dry stack, in-pit, backfill [T19][O22]. This materially changes the monitoring profile: a dry stack has no supernatant pond, so M05 is largely inapplicable and risk shifts to slope stability, dust and seepage. The registry must gate analytics by storage method, not just by consequence class.
- Geometry: crest polygon, embankment toe, **calculation boundary** (the crest-line polygon used to bound pond area computation [O22]), beach extent, spillway/decant structures, penstock towers, training walls, downstream receptor zones.
- Design criteria: design freeboard, minimum beach length, design storm, target phreatic surface, dam raise schedule.
- Consequence classification and downstream exposure (population, watercourses, infrastructure).
- Assigned accountable roles — Engineer of Record, Responsible Person, Accountable Executive equivalent.

**Notes.** AOI geometry should reuse the existing KML AOI approach already in use on VINIS. Consequence class drives service tier: [O22] is explicit that Sentinel-2 pond monitoring is appropriate for large facilities with high consequence of failure and unsuitable below a size threshold, so classification must gate which analytics are even offered.

**Outputs:** facility record, AOI geometries, monitoring plan template, per-site calibration parameter set (see M08).

---

### M02 — Earth Observation Ingestion

**Purpose.** Scheduled, resilient acquisition and pre-processing of all space-based and forecast data.

**Sub-streams:**

| Stream | Level 1 (free) | Level 2 (commercial) | Primary use |
|---|---|---|---|
| SAR / InSAR | Sentinel-1, 12-day, ~25 m [T19] / 5×20 m grid [L19] | COSMO-SkyMed (~US$1 200/km²/yr [L19]), other high-res SAR | Deformation, water extent, DTM |
| Optical multispectral | Sentinel-2, 5-day, 10 m, L2A surface reflectance [O22] | Sub-metre (PeruSat-1, Maxar/WorldView-2 53 cm [T19]) | Pond, vegetation, iron oxide, change detection |
| Weather forecast | NOAA GFS, ~10-day lead [L19] | ECMWF, up to 15-day lead, ~US$2–10k/yr [L19] | Overtopping and runoff forecasting |
| Seismic | Public catalogues | Regional networks | Liquefaction trigger context [T19] |

**Core functions:**
- Orbit/track management — ascending vs descending, incidence angle recording (Cadia used descending, ~33° [T19]). Line-of-sight sensitivity is orientation-dependent, so track selection is a per-site design decision [L19].
- Cloud and cloud-shadow screening using scene QA metadata plus visual confirmation [O22].
- Atmospheric correction — L2A surface reflectance required for any multi-date comparison [O22].
- Scene rejection log (e.g. [T19] removed a 14 April 2018 scene for atmospheric anomalies) — rejections are an audit artefact, not a silent operation.
- Acquisition latency tracking. [O22] shows temporal separation degrades accuracy directly; comparisons under two days stayed within the ±20 000 m² band.

---

### M03 — In-Situ Ingestion: IoT Telemetry & Manual Inspection

**Purpose.** The ground truth half of the fusion. Two distinct sub-modules under one contract.

**M03a — IoT & Instrumentation Telemetry**
- Piezometers (phreatic surface), inclinometers, settlement/survey monuments, decant and drain flow meters, turbidity, weather station, GNSS receivers (1–2 mm accuracy [L19]), CCTV and time-lapse, ground-based InSAR, UAV survey products.
- Device health and liveness monitoring. [L19] notes GNSS installations cost US$60k–120k and are exposed to vandalism and theft; [T19] notes instrumentation output must itself be reviewed to confirm the equipment is measuring correctly.
- Automated interlocks where warranted — the tailings pipeline rupture → delivery line shutdown example in [T19].
- Store raw + calibrated + drift-corrected values separately.
- **Design for relocation.** An active tailings dam is under continuous construction, and DAMSAT found this materially restricts where fixed monitoring equipment can be installed and kept [D21]. Fixed assets must be treated as movable, with a documented relocation history, because a monument that moved is indistinguishable from ground that moved unless the move is recorded. Instrument position history is a first-class record in M01, and relocation is a ledger event under M14.
- **Deployment logistics are a real constraint.** DAMSAT's in-situ programme slipped at three of its sites, and equipment import required in-country partners to move at all [D21]. Assume long lead times on hardware and price them in.

**M03b — Manual Inspection Capture**
Mirrors the inspection hierarchy in [T19]:
- Daily operator visual inspection sheets
- Specialist geotechnical/civil inspections
- Third-party review and audit

**Inspection form taxonomy — adopt this as the canonical schema.** [Z24] reproduces a seven-category visual performance parameter set (after Clarkson & Williams 2020) that is materially more complete than anything in v0.3:

| Category | Parameters |
|---|---|
| **Tailings surface** | Slurry flow rate and density; size and position of decant pond; reservoir level and freeboard; tailings transport conditions; beach slopes; persistent vortex in the reservoir |
| **Embankment / berms** | Cracking; bulging at toe of slope; weeping; piping; sloughing; subsidence at crest or downstream embankment; sinkholes at upstream face; new or changed vegetation; surface erosion; wave erosion on upstream face |
| **Seepage flow** | Trench flow efficiency; density and flow rate of slurry; new or changed seepage areas; **colour of seepage water**; quantity, location and clarity of flowing water |
| **Ancillary infrastructure** | Decant facility integrity and access; cracking in concrete structures; erosion in roadways and access routes; return water storage capacity; condition of gates, fencing, signage; pump and pipeline condition; vegetation clogging drainage ditches; discharge tunnel or conduit condition, seeps, cracks |
| **Emergency preparedness** | Status of leak detection system; performance of automatic flow measurement; status of fault alarms |
| **Instrumentation** | Water levels at permanent monitoring stations; **condition of monitoring instruments and data reading quality** |
| **Miscellaneous** | Animal burrows |

Two structural observations. First, **the monitoring system is itself an inspected object** — two of the seven categories are about the instrumentation and alarms, not the dam. That gives M08's device-health function a standards basis rather than a nice-to-have status. Second, several parameters are *qualitative and colour-based* (seepage water colour, vegetation lushness, water clarity), which means the mobile capture form needs structured photography with controlled reference, not free-text.

**Inspection level taxonomy.** ANCOLD's five levels — comprehensive, intermediate, routine, special, emergency — via [Z24], with routine frequency indexed to consequence category. ICOLD B158 gives a concrete default: **weekly routine, moving to daily after a major or extreme event.** Adopt the five-level model in M01's monitoring plan and let consequence class drive frequency.

**ISO definitions to adopt verbatim** [Z24]: *monitoring* is determining the status of a system, process, product, service or activity; *inspection* is determining conformity to design specification. These are different data types with different downstream uses and should not be conflated in the schema.

Requirements: offline-first mobile capture, geotagged and time-stamped photography, structured observation forms keyed to the taxonomy above and to the warning-sign mapping in M09, inspector identity and competency record, and a chain of custody so an inspection cannot be silently edited after the fact.

*This sub-module is the direct analogue of the Medicoach AMS pattern — structured field capture, role-based submission, immutable record — and can likely reuse that stack.*

---

### M04 — InSAR Deformation Processing & Time Series

**Purpose.** Millimetric displacement history across the embankment.

**Pipeline** (after [T19]):
1. Interferogram network formation across all scenes in the epoch (81 descending Sentinel-1 scenes used pre-collapse at Cadia).
2. Adaptive spatial averaging — noise reduction while preserving feature boundaries.
3. Atmospheric correction via long-wavelength spatial filtering.
4. Phase unwrapping with visual **and** statistical QC; discard inconsistent regions.
5. Stack unwrapped interferograms into a per-pixel displacement time series.
6. Epoch management — separate pre-event and post-event epochs; deformation *during* a large surface change cannot be measured, but the resulting low-coherence zone delineates the failure extent.

**Coherence management (critical, feeds M08):**
- Seasonal vegetation cycling changes coherence materially — at Cadia, winter-spanning interferograms outperformed summer ones.
- Wet tailings pond areas lose coherence entirely.
- Temporal and spatial decorrelation drivers per [L19]: vegetation movement, water content change, soil property change, baseline geometry.

**Outputs:** displacement maps between arbitrary date pairs, mean displacement rate maps, per-pixel time series, coherence masks, uncertainty estimate derived from stable-area statistics (3.3 mm @ 95% at Cadia).

**Product classification.** [L19] specifies a three-category physical stability output: below threshold/undetectable · possibly significant · likely significant. Adopt this as the standard classification, with site-specific thresholds.

**Automated vs supervised processing — a service-model decision, not just a data decision.** DAMSAT ran these as separate products: automated InSAR analysis on Sentinel-1 in the basic tier, and *supervised* InSAR analysis on COSMO-SkyMed in the premium tier [D21]. The distinction matters commercially. High-precision InSAR still requires an expert in the loop, so premium InSAR carries a recurring labour cost, not only a data licence cost. Price the two tiers accordingly and do not promise premium-grade interpretation on an automated pipeline.

**Output form.** DAMSAT presents movement as line-of-sight velocity maps plus point-movement time series graphs [D21]. Adopt both — the map answers "where", the graph answers "is it accelerating", and M10 needs the second.

---

### M05 — Water, Beach & Freeboard Analytics

**Purpose.** Quantify supernatant water. Water quantity and pond position drive both likelihood and consequence of failure [O22], and short beaches inhibit the solar desiccation that strengthens tailings.

**Metrics:**
- **NDWI** = (Green − NIR) / (Green + NIR), pixel-wise, range −1 to 1, direct correlation with moisture [O22].
- **NIR reflectance** — water absorbs in NIR while dry land and vegetation reflect, so low NIR indicates surface water. Reported to give better water-edge contrast than NDWI, and at Cadia the NIR-based areas agreed slightly better with validation imagery [O22].
- Pond area = sum of qualifying pixels **inside the M01 calculation boundary**.
- Derived: pond centroid and its proximity to the perimeter wall (the Merriespruit mechanism), beach length/width, freeboard against design criteria.

**Threshold calibration sub-process (mandatory per site, per P4):**
1. Define a transect crossing both beach and pond.
2. Extract monthly profiles across a full year to capture seasonal variability [O22].
3. Establish conservative upper and lower bounds. Reference values at the Impala Dam 4 site: NDWI 0.05–0.40; NIR 500–1 000 (0.05–0.10 native reflectance).
4. Acquire independent very-high-resolution validation imagery; delineate the pond manually.
5. Compute area mismatch AM = A_validation − A_satellite across candidate thresholds.
6. Select the threshold minimising |AM| across the validation set, weighted toward the low-sensitivity side of the mean curve. Reference adopted values: NDWI 0.11, NIR 870.
7. Record derivation, validation dates, and residual error in the site calibration record.

**Fitness-for-purpose gate.** Expected accuracy is within ±20% of validation area (half within ±10%), with absolute discrepancies reaching ~20 000 m². The module must refuse to publish pond area for facilities where this error exceeds a configured fraction of typical pond area, and must state the achieved band on every output.

**Known edge cases:** anomalous profiles at low pond level with exposed sandbanks; pond edges against a gently sloping beach are inherently fuzzier than edges against a training wall.

**Beach measurement — gap closed in v0.3.** [O22] flagged beach length as unresolved future work. DAMSAT built it: a dedicated Tailings Beach module computing **minimum distance from water to dam crest** and **beach area inside the pond**, from 10 m Sentinel-2, explicitly scoped to upstream and centreline raised dams [D21]. Two design consequences:

- *Minimum water-to-crest distance* is the operationally meaningful metric, not mean beach width. It is a minimum over the pond perimeter, so it is sensitive to pond migration as well as pond size — which is the Merriespruit mechanism made measurable.
- The upstream/centreline scoping ties directly to M01's construction-method field. A downstream-raised facility gets a different beach specification, and a dry stack gets none.

The ±20 000 m² pond-area error band from [O22] propagates into the derived beach and freeboard figures. Publish the propagated uncertainty, not just the headline distance.

**Freeboard — gap closed structurally in v0.4.** Open question #2 asked how freeboard should be quantified. ANCOLD via [Z24] supplies the decomposition. Freeboard is not one number; it is a stack of allowances measured up from the tailings surface to the crest:

```
CREST
  ├─ Flood spill depth
  ├─ Contingency storage allowance
  ├─ ── Maximum operational level ──
  ├─ Extreme storage allowance
  ├─ Wet season storage allowance
  └─ Minimum decant storage allowance
TAILINGS SURFACE
```

Defined terms: *minimum decant storage allowance* — minimum water needed to achieve the required discharge condition; *wet season storage allowance* — volume permitted during wet season including rainfall and process input; *operational freeboard* — crest to top of tailings material; *maximum operating level* — highest normal-condition water level before the site emergency plan activates. "Beach freeboard" and total "freeboard" are separate spans across this stack.

Three consequences for the module. **(a)** M01 must hold all five allowances per facility as design criteria, not a single freeboard number. **(b)** There is a *lower* bound as well as an upper one — CDA notes water must be kept below the maximum level **and above** the minimum recommended level after discharge [Z24], so an over-drained pond is also a reportable deviation. **(c)** The numeric values per consequence category remain unverified; the structure is usable now, the thresholds are not.

**Why this is the highest-value analytic in the stack.** [Z24] states plainly that overtopping is the greatest cause of failure and that freeboard monitoring therefore has high potential for preventing that failure mode. The modern-era failure data reinforces it — see M09.

**Beach criteria now quantified.** From [Z24]:
- ICOLD Bulletin 181 collected global beach slope data and confirmed **beach length is the inverse of beach slope**; recommended overall beach slope is **1–4%** for uniform material distribution.
- Beach angle matters most for **upstream raises**, because the next lift cannot be constructed on a wet surface — beach distance must be long enough to provide evaporation surface area.
- Delayed settlement manifests as tension cracks in the embankment; incorrect beaching creates a dusting problem in arid settings.
- **Jeong & Kim (2020)** correlated design factor of safety against current beach distance from UAV-derived digital models to derive a *critical beach distance* for safe operation under varying weather, at a FoS target of 2.0. **This is the method for converting a measured beach distance into a safety criterion** rather than a bare number, and it is the natural bridge from M05 into M17. The model is geometry-specific and needs constant updating — which is an argument for our continuous measurement rather than against the method.
- Manual beach measurement is discouraged where it requires walking onto the deposit [Z24], which is a safety argument for remote measurement independent of the cost argument.

---

### M06 — Chemical Stability & Vegetation Analytics

**Purpose.** Detect the slow-motion pollution problem, which [L19] identifies as an externalised cost that typically only surfaces after closure.

**Functions:**
- Iron oxide index trending as a proxy for acid mine drainage and seepage [L19].
- Vegetation stress indices — heavy metal loading alters spectral signature [L19].
- Water discolouration and turbidity change detection downstream.
- Seepage location and rate change; movement of the vegetation line as a phreatic surface proxy.
- Soil moisture change in the dam body.

**Constraints to encode:** an affected area of at least several square metres is required for detection; cloud obstruction and daily-rather-than-hourly temporal resolution limit responsiveness [L19].

---

### M07 — Change Detection & Site Activity

**Purpose.** Detect what is happening on site, independent of what is reported. This is the module that most directly serves the audit function.

**Detects:**
- **Dam raise / construction activity.** WorldView-2 imagery on the morning of the Cadia failure showed newly graded surface and plant on the dam wall — activity that was not communicated by the operator [T19]. Undeclared or off-schedule raises are a first-order audit finding.
- Spillway and diversion modification, gradual infilling of diversions [L19].
- Phased failure detection — [T19] identified an initial failure and a secondary failure at least two days later from successive Sentinel-2 scenes, which the operator statement did not convey.
- Footprint growth, new deposition points, haul road and stockpile change.
- Post-event extent mapping (including low-coherence delineation from M04).

**Output type:** discrepancy events — observed state vs declared state — routed to M14.

---

### M08 — Data Quality & Uncertainty Engine *(cross-cutting)*

**Purpose.** No number leaves the platform without provenance and an error bar (P5).

**Functions:**
- Per-measurement uncertainty: InSAR from stable-area statistics; pond area from validation residuals; instrumentation from calibration records.
- Coherence and cloud masking as published layers, not hidden filters.
- Temporal separation tracking between fused observations, with degradation warnings [O22].
- Sensor cross-validation — InSAR vs GNSS vs survey monuments; satellite pond area vs decant flow and level instrumentation.
  - **Reality check.** DAMSAT set out to do exactly this and did not achieve it within the project. The stated cause was inability to obtain permission to install instruments on active mine sites, compounded by COVID-19 restrictions [D21]. This is the most useful negative finding in the corpus: the hardest part of fusion is not the algorithm, it is commercial access. Treat cross-validation coverage as a **contractual precondition** written into the operator engagement, and report the fraction of facilities where it is actually achieved as a platform-level KPI. Where it is not achieved, satellite outputs must be published with a wider uncertainty band and an explicit "unvalidated against ground truth" flag.
- Gap and outage register: acquisition failures, cloud loss, coherence loss, device offline. A gap is a reportable condition, not silence.
- Site calibration parameter registry with version history and derivation evidence.

---

### M09 — Failure Mode & Warning Sign Engine

**Purpose.** The semantic core. Translates observations into failure-mode-relevant meaning (P3).

**Failure mode taxonomy** [L19]: overtopping · mass movement · internal erosion (dam fill and foundation) · surface erosion. Extended with the causal distribution from Rico et al. 2008 via [T19]: foundation, slope instability, overtopping/overflow, mine subsidence, unusual rain, snowmelt, piping/seepage, seismic liquefaction, structural, management/operation.

**Structure — a four-column mapping per [L19]:**

| Failure mode | Adverse condition | Warning sign (rapid / gradual) | Observable |
|---|---|---|---|
| Overtopping | Loss of spillway capacity | Blockage or failure of spillway / gradual infilling of diversion | Optical change detection (M07) |
| Overtopping | Freeboard below design | Rising effluent level | Pond area & freeboard (M05) |
| Mass movement | Loss of crest elevation | Crest geometry change, cracking / settlement | InSAR (M04), inspection (M03b) |
| Mass movement | Loss of upstream erosion protection | Slope geometry change, cracking | InSAR, optical |
| Internal erosion | Dam fill or foundation erosion | Sinkholes, turbid seepage / increasing seepage turbidity | Optical, vegetation, inspection, turbidity IoT |
| Surface erosion | Slope erosion | Seepage-induced sloughing / raised saturation | Vegetation line, piezometers, InSAR |

**Trigger, Action, Response Plan (TARP) layer.** Each observable carries site-specific green/amber/red thresholds bound to a defined action and a named accountable role. This is where the traffic-light state in the [L19] prototype interface is actually generated.

**A published four-level scale to build TARPs from.** MAC's displacement risk scale via [Z24] is the only concrete, tiered, published example in the corpus and should be the template for the schema even where the values differ per site:

| Level | Condition |
|---|---|
| Acceptable | Deformation not visible, or within designed range |
| Low risk | Visible and exceeds the designed limit |
| Moderate risk | Toe displaced in the form of sloughing; movement exceeds historical data |
| High risk | Sloughing occurs and travels more than 3 m from original location |

Note the structure: level 1 is defined against **design**, level 3 against **history**, level 4 against an **absolute** value. A TARP schema must support all three comparator types, not just fixed thresholds.

**Reweighting the failure-mode priors.** [Z24] presents failure causes split into 1910–1999 and 2000–2018 (data from Lyu et al. 2019). Normalising the raw counts by period length gives approximate failures per year:

| Cause | 1910–1999 | 2000–2018 | Direction |
|---|---|---|---|
| Overtopping | ~0.50/yr | ~0.95/yr | **Roughly doubled** |
| Seepage | ~0.58/yr | ~0.74/yr | Up |
| Foundation failure | ~0.48/yr | ~0.53/yr | Flat |
| Earthquake | ~0.52/yr | ~0.26/yr | **Roughly halved** |

*Normalisation is mine, not the source's — [Z24] presents raw counts over unequal periods, which overstates the historical column. Treat the direction as indicative and verify against Lyu et al. 2019 before using externally.* The implication is nonetheless consistent with the climate argument in M10: **overtopping and seepage now dominate, and seismic causes have receded relative to them.** That reweights M17's FMEA priors toward water management — which is where M05 lives, and where the cheapest satellite observables are.

**A specific classifier hazard.** [Z24] records that the initial stages of overtopping can be mistaken for signs of water erosion. Since these carry very different urgencies and both present as surface change in optical imagery, M07 and M09 must treat overtopping-versus-erosion as a known confusable pair, flag it explicitly rather than picking silently, and route ambiguous cases to human review.

**Epistemic caution — read this before over-promising.** After three years of operational deployment across 31 sites, the DAMSAT team's own assessment was that monitoring tailings dam displacement remains a young discipline, that they were still learning how to convert it into a warning system, and that **the links between failure modes and movement precursors are not yet well understood** [D21]. That is the team that ran the reference implementation, saying so at project close. The mapping table above is a working hypothesis with case-study support, not settled science. Two consequences: warning thresholds must be revisable per site without a code change, and external claims about predictive capability must stay inside what [T19]'s single well-documented case actually demonstrates.

**Warning time is not a constant.** CDA via [Z24] puts the range at days, months, years — or hours to minutes. Mount Polley is cited as having presented no warning signs at all. The system must be honest that some failure modes are not preceded by anything it can see.

---

### M10 — Predictive & Risk Models

**Purpose.** Forward-looking risk, several weeks ahead where achievable [L19].

**Models:**
1. **Inverse velocity failure timing** (Carlà et al. 2017, applied in [T19] and [L19]). Plot inverse of deformation rate against time; linear extrapolation to zero estimates failure time. At Cadia this predicted failure roughly two days ahead; the Tre-Altamira analysis reported in [L19] suggested up to nearly a month of lead time on the same event. Model must publish fit quality and the alternative-hypothesis check — deceleration is as important as acceleration (post-collapse Cadia rates were decreasing, indicating possible stabilisation).
2. **Overtopping forecast model** — weather forecast → runoff → freeboard consumption [L19].
3. **Probability of dam failure model** — combining failure mode indicators, construction method, phase, condition history. Baseline context: annual failure probability of order 1.2% across a large mine-site inventory, roughly two orders of magnitude above conventional water-retaining dams [L19].
4. **Probability of pollution incident model** [L19].
5. **Saturation / phreatic surface change model** driven by forecast rainfall and moisture indicators.

**Design rule 1 — detect regime change, not magnitude.** Precursor signatures are non-monotonic. Cadia showed transient low-magnitude signals migrating along the dam for months, a steady low-level signal at the inner edge attributable to consolidation, then a distinct regime change in mid-to-late January 2018 with ~29 mm over five weeks [T19]. 10–20 mm over nine months was benign; the same order of movement over five weeks was terminal.

**Design rule 2 — treat the climate as non-stationary.** Intense rainfall is linked to roughly a quarter of global and a third of European tailings dam failures, and extreme rainfall frequency is increasing even in regions where mean annual rainfall is projected to fall [L19]. Historic rainfall statistics therefore systematically understate current risk. The overtopping and saturation models must accept forward-looking climate scenarios as an input, and the design-storm assumption held in M01 must carry a review date rather than being treated as fixed for the life of the facility. [A25] reinforces this qualitatively but adds no method.

**Design rule 3 — all models are governed by M18.** No model reaches a live alert path without passing the validation, calibration and explainability gates.

**A concrete forcing-to-response lag to build on.** Duan et al. (2023), catalogued in [Z24], applied GPU-assisted InSAR time series to 91 images over 2019–2022 and found rainfall significantly influences tailings pond deformation, with **peak deformation rate lagging maximum rainfall by approximately one month.** If that lag generalises, it is directly actionable: it converts a rainfall forecast into a deformation forecast with a usable horizon, and it means a wet season is a *scheduling* signal for intensified surveillance, not just a real-time trigger. Test the lag per site before relying on it — it will depend on permeability, drainage design and dam height.

**⚠ Unresolved base rate — affects M10, M17 and M18.** Two sources in the corpus read the same underlying Azam & Li (2010) figure differently:
- [L19] renders it as an **annual probability of failure of 1.2%**.
- [Z24] renders it as **the percentage of the total population that failed across 100 years of data — 1.2% cumulative**, paired with 0.01% for conventional water dams.

These differ by roughly two orders of magnitude and cannot both be right. The cumulative reading is more consistent with the convergent incident-count evidence: [Z24] cites roughly three tailings dam failures annually, [T19] cites two reported plus two unreported per year, [L19] cites about 20 per decade. Against a global population in the thousands, that is nowhere near 1.2% per year. **Working assumption: the 1.2% figure is cumulative over ~100 years, and the annual rate is far lower.** This must be resolved against Azam & Li (2010) directly before any QRA output or ROI claim is published — and note that the lower the true base rate, the *stronger* M18's argument against accuracy as a metric.

---

### M11 — Consequence & Impact Modelling

**Purpose.** Convert probability of failure into consequence, which is what drives regulatory and community action.

**Named models available (v0.3).** DAMSAT's Emergency Planning module is built on two specific tools worth evaluating rather than rebuilding [D21]:
- **EMBREA-MUD** — dynamic simulation of tailings embankment breaching, representing water and dam material as three distinct layers. Presented as a novel capability with no prior equivalent for tailings.
- **Life Safety Model (LSM)** — agent-based loss-of-life simulation, applied by DAMSAT to tailings dams for the first time (previously water dams only), and reported to perform well against historical failures. Lumbroso is a co-author on the underlying LSM literature cited in [L19], so the two sources are the same lineage — corroboration, not independent confirmation.

Both are HR Wallingford products. Build-vs-licence is a genuine decision here; breach modelling for non-Newtonian tailings is specialist work and building it is not obviously the right use of capital.

**Functions:**
- Breach and runout modelling → inundation outlines. Released volume is typically around one fifth of stored volume [L19].
- Loss of life estimation. [L19] notes that tools exist but are rarely used, and where used are often simplistic; also that health impacts from contaminated food, water and soil are seldom accounted for.
- **Site data acquisition is the bottleneck, not the modelling.** DAMSAT reported that consequence work depends on good site information, that obtaining it was slow, that mapping the bureaucratic route to it took a long time, and that in some cases the information was not public or did not exist [D21]. Budget for data archaeology, and design M01 so a facility can carry a partial consequence model with the gaps declared rather than blocking on completeness.
- Downstream receptor mapping: population, settlements, water abstraction points, agricultural land, ecologically sensitive areas, infrastructure.
- Differential exposure analysis. [L19] documents that women are disproportionately affected in low-income settings through water collection burden and contamination exposure — the receptor model should not treat exposed population as homogeneous.
- Financial and reputational exposure modelling for the operator, insurer and investor views (market capitalisation impacts are documented in [L19]).

---

### M12 — Alerting, Escalation & Accountability

**Purpose.** Directly answers P2. Alerts that die in an inbox are the observed failure pattern.

**Validated design from the reference implementation.** DAMSAT's delivered Warnings module [D21] confirms most of what v0.1 specified, and is worth matching: three levels (none / medium / high); thresholds configurable **per module and per site**; each warning carrying creation time, modification time, origin and reason; and warnings raisable or lowerable in light of ground information or actions taken. One deliberate divergence — DAMSAT permits selected users to modify threshold values. Retain that capability, but under M14 a threshold change is an immutable ledger event with actor and justification. Silently relaxing a trigger is precisely the governance failure the platform exists to catch.

**Live incident coordination.** DAMSAT's user requirements work surfaced needs that pre-event alerting does not cover [D21]: assessing a situation objectively *before reaching site*, establishing what caused an event and where it originated, determining the extent of the affected area, and — named twice — working smoothly with other organisations during emergency response. Add a live incident mode: a shared operating picture across operator, regulator, emergency services and downstream authorities, with a single event timeline that all parties see and contribute to. This is distinct from M11, which is pre-event planning.

**Functions:**
- Multi-channel notification (in-app, email, SMS) with the alert management verbs from the [L19] prototype: issue SMS · clear alert · issue action instruction · contact site owner.
- **Named-recipient escalation.** Each alert class routes to a named role with authority to act, not a mailbox. Unacknowledged alerts escalate on a timer to the next authority level and ultimately to the accountable executive and, where configured, the regulator.
- Acknowledgement, disposition and justification capture. If a flagged condition is dismissed, the dismissing party, reason and timestamp are permanently recorded — this is the control against "concerns overridden by financial pressure".
- Community and downstream warning integration for high-consequence facilities. Samarco residents had roughly 25 minutes in some cases despite ten hours of internal knowledge [L19]; warning dissemination is a system requirement, not a courtesy.
- Alert suppression rules with mandatory expiry and audit trail.

---

### M13 — Action & CAPA Workflow

**Purpose.** Close the loop between detection and remediation.

**Functions:** work order generation from alerts and inspection findings; assignment, due dates, priority tied to failure-mode severity; evidence-of-completion capture (photo, survey, instrument reading); verification and sign-off by a competent person; overdue action register with escalation; recurrence detection; scheduling of routine inspections, specialist reviews and independent third-party audits [T19].

---

### M14 — Audit, Assurance & Conformance ⭐

**Purpose.** The commercial differentiator. Everything above generates evidence; this module makes it defensible to a regulator, an insurer, an investor and a court.

**Functions:**
- **Immutable evidence ledger.** Append-only record of every observation, alert, acknowledgement, dismissal, action and configuration change, with actor identity and timestamp. Threshold changes are logged as events — silently relaxing a TARP trigger must be visible.
- **Declared vs observed reconciliation.** Systematic comparison of operator disclosures against M07 change detection, M04 deformation and M05 pond metrics. The Cadia dam-raise activity and the phased failure are the archetypal findings [T19].
- **Conformance framework mapping.** The primary anchor is the **Global Industry Standard on Tailings Management (GISTM)**, ICMM with UNEP and PRI, August 2020. Structure per [Z24]: three layers — **topics → principles → requirements** — across six topics, plus flood and earthquake design guidance and a consequence classification matrix. The wider instrument landscape is in §0.5. *Control-level mapping must be built from the primary text; §0.5 is an acquisition plan, not a conformance mapping.*

**GISTM six topics and what each demands of the platform** (from [Z24]'s summary; verify against primary text):

| Topic | Requirement highlights | Platform implication |
|---|---|---|
| **I — Affected Communities** | UNGP alignment; consultation in management decisions; grievance mechanism; FPIC where indigenous rights are affected | M15's community tier is a conformance obligation, not goodwill |
| **II — Integrated Knowledge Base** | Interdisciplinary base covering social, environmental, local economic and technical context; climate change uncertainty captured; site characterisation (climate, geomorphology, geochemistry, hydrology); **breach analysis estimating impacted area, flow arrival times, depth and velocities**; **updated at least every five years**; multi-criteria alternatives analysis | M01 plus M11 are the digital instantiation of the knowledge base; the five-year refresh is a schedulable obligation |
| **III — Design, Construction, Operation & Monitoring** | Consequence classification; explicit design criteria including factors of safety for slope stability and seepage control; **Engineer of Record** and **Design Basis Report**; **construction vs design intent verification (CDIV)**; construction records report on significant alteration; OMS manual for all staff levels; and decisively — **monitoring systems that check specified assumptions and monitor probable failure mechanisms, with precise and quantifiable performance goals, metrics and parameters across the whole facility lifetime, evaluated at suitable intervals and used as proof of variance from anticipated performance and of deterioration over time** | **This is the product specification, written into the governing standard.** It is also the design-vs-observed axis in explicit form. Cite it verbatim in sales material once verified. |
| **IV — Management & Governance** | Board-adopted policy; **Responsible Executive accountable directly to the CEO**; EOR appointed under written agreement defining authority and duties, with a handover plan on change; **risk assessments revised at least every three years** and on significant context change; **whistleblower protection — formal, private, documented complaint procedure with no retaliation**; recognise and reward staff who report issues | Every item is a ledger-able event. M12's named-escalation design and M14's dismissal logging map onto the Responsible Executive and whistleblower requirements directly. |
| **V — Emergency Response & Long-Term Recovery** | Site-specific EPRP with defined test and update frequency; community-focused preparation; collaboration with public sector, first responders and local government; long-term recovery planning; public access to post-failure results | M11 plus M12's live incident coordination mode |
| **VI — Public Disclosure & Access** | Publish and routinely update; plain-language design summary for new facilities; for existing facilities publish **annual performance and environmental/social monitoring summary, EPRP summary, dates of most recent and next independent reviews, and annual confirmation of adequate financial resources**; respond promptly to stakeholder enquiries; support international transparency databases | **Topic VI is a report template.** M16 should ship a GISTM Topic VI disclosure pack as a first-class output. |

- **The disclosure gap, quantified.** The Church of England / Investor Mining and Tailings Safety Initiative survey (May 2021, via [Z24]) contacted **721 companies**. 341 responded at all; 188 of those declared no TSF; only **114 provided full disclosure** (153 including partial); 183 did not respond. The evidentiary case for independent observation sits in that one table — most of the industry did not fully disclose even under coordinated investor pressure. It also shows the investor lever working, which is the M15 EPFI/DFI argument in practice.
- **The CDA closed-loop state machine.** [Z24] reproduces CDA's dam safety management system as an explicit loop: routine surveillance and periodic dam safety reviews → *are requirements met?* → *is it safe?* → *can it readily be made safe enough?* → develop improvement options → improve the dam / reduce consequences / accept and manage risk → *dam is safe enough* → operate, maintain and prepare for emergency → back to surveillance. **Implement M13 and M14 as this state machine rather than as generic ticketing.** It is a published model from the guideline [Z24] reports as most used in the industry, and modelling it directly makes conformance demonstrable by construction.
- **Design vs observed reconciliation — the second axis.** CDA's four-purpose surveillance definition states it plainly: compare actual against design performance to identify deviations; detect changes in performance or development of hazardous conditions; confirm operations follow dam safety requirements; confirm adequate maintenance [Z24]. GISTM Topic III requires the same in the language of checking specified assumptions. This is where the Engineer of Record's liability sits, and it is the more diagnostic of the two reconciliation axes.
- **Immutable evidence ledger.** Append-only record of every observation, alert, acknowledgement, dismissal, action and configuration change, with actor identity and timestamp. Threshold changes are logged as events — silently relaxing a TARP trigger must be visible.
- **Declared vs observed reconciliation.** Systematic comparison of operator disclosures against M07 change detection, M04 deformation and M05 pond metrics. The Cadia dam-raise activity and the phased failure are the archetypal findings [T19].
- **Monitoring adequacy assessment.** Automated check that the deployed monitoring for a given facility actually covers its credible failure modes — the direct control against "monitoring not focused on potential failure modes" [L19]. Now formally testable via M17: **every failure mode with an RPN above the site threshold must have at least one live observable with an acceptable detection rating.** A failure mode with high severity, non-trivial occurrence and no monitoring coverage is an automatic red finding.
- **Jurisdictional and portfolio benchmarking.** Comparative conformance across facilities, operators and jurisdictions. [A25] attempts this and gets it wrong by comparing raw failure counts across regions without normalising by facility population — the platform must normalise by facility-years of exposure, and must state the denominator on every comparative figure it publishes.
- **Independent review scheduling and register.** Enforce periodic specialist and third-party review, with findings and closure tracked.
- **Regulator and third-party evidence packs.** Point-in-time exportable, tamper-evident bundles.
- **Portfolio assurance view.** Cross-facility conformance scoring for owners with multiple TSFs and for regulators overseeing a jurisdiction. [L19] notes regulators typically oversee far more facilities than they have staff to inspect, and are otherwise dependent on the quality of operator-produced reports.

---

### M15 — Stakeholder Portal & Access Tiers

**Purpose.** Differentiated access to a common evidence base (P7).

**Personas and default entitlements:**

| Persona | Access |
|---|---|
| Operator / TSF manager | Full detail, all raw data, configuration rights |
| Engineer of Record / geotech specialist | Full technical detail, interpretation and sign-off rights |
| Corporate / accountable executive | Portfolio risk view, escalations, conformance status |
| Regulator | Full monitoring record, conformance status, audit packs, alert history including dismissals |
| Independent reviewer / auditor | Read-only full history, evidence ledger |
| Insurer / investor | Risk grade, conformance status, trend, no proprietary operational data |
| NGO / civil society | Published transparency layer — status, pollution indicators, incident record |
| Downstream community | Alert subscription, plain-language status, evacuation information |

**Service tiering — validated commercially.** DAMSAT shipped exactly two packages [D21]. *Basic* ("minimum leave behind") on free data only — Sentinel-1 InSAR, Sentinel-2 visualisation, leakage and beach, NOAA GFS forecast, plus warnings. *Premium* adds supervised COSMO-SkyMed InSAR, GNSS, ECMWF, and emergency planning. Two lessons carry directly across:

- **No free tier.** DAMSAT deliberately declined to offer one, on the reasoning that end-users value a service more when they have paid for it [D21]. Basic was priced as low as possible to drive uptake, but never at zero. For a platform whose value proposition is institutional behaviour change, this is a sound instinct.
- **Basic carries a floor cost.** Software maintenance and data storage do not go away because the satellite data is free; additional facilities and features are separately costed. Model the run-and-maintain floor per tenant before pricing.

**Segments to add (from [D21]'s commercialisation analysis).** v0.2's persona list was operations-facing and missed the capital side entirely:
- Tailings dam construction **EPC contractors**
- **Remediation and monitoring consultants** — a channel, not just an end user
- **Insurance and catastrophe risk industry**
- **Institutional investment funds** and ESG screening
- **Equator Principles Financial Institutions and development banks**
- International donor-funded agencies (e.g. UNEP)

The EPFI and development-bank segment is the strongest structural lever in the list and is absent from v0.2. Project finance conditionality can compel monitoring where regulation cannot, particularly for the junior and mid-tier operators [L19] identifies as the highest-risk population. It also aligns with donor framing: DAMSAT mapped its outcomes to SDG 1.5.1 (disaster deaths and affected persons per 100,000) and SDG 6.3.2 (ambient water quality) [D21], which is the language DFI mandates are written in.

**Data confidentiality reconciliation — an unsolved problem, flagged prominently.** DAMSAT reported difficulty engaging public and private institutions in the same programme because of data transparency concerns, and made confidentiality management an explicit agenda item in its stakeholder negotiations [D21]. Differentiated RBAC does not resolve this; it is a commercial and legal problem. The module therefore needs an explicit **data-sharing governance layer**: per-data-class disclosure rules, operator consent regimes with defined scope and duration, defined lawful-disclosure triggers where withholding would endanger life, and a clear statement of what a regulator sees by default versus by request. **Get this wrong and operators do not participate, which collapses P1 to satellite-only and P7 to nothing.** It should be resolved contractually before the first operator pilot, not during it.

---

### M16 — Visualisation, Reporting & API

**Purpose.** Delivery surface.

**Functions:**
- Map-first browser interface with per-facility traffic-light status; click-through from status to cause to underlying evidence [L19].
- Time series explorer — displacement, pond area, freeboard, piezometric level, rainfall on a common axis. Fusion is only visible if the charts share a timeline.
- Consequence overlay toggles: breach outline, population impact, critical pollution receptors [L19].
- Imagery comparison — current vs historical, with change annotation.
- Scheduled reporting: daily operator brief, monthly technical report, annual conformance report, GISTM-style disclosure export.
- API and webhooks for SCADA, GIS, ERP and regulator systems.
- Mobile view for field and executive users.

---

### M17 — Structured Risk Assessment (FMEA / FTA / QRA / Bowtie) *(new in v0.2)*

**Purpose.** The formal, expert-driven risk analysis that *defines what the platform should be watching*. This was a gap in v0.1: M09 mapped observables to failure modes, but nothing determined which failure modes mattered most at a given facility or whether monitoring coverage was adequate.

**Methods supported:**

- **FMEA** — enumerate failure modes, score Severity × Occurrence × Detection to produce a Risk Priority Number. Structural failure, piping and seepage are typically the highest-RPN modes [A25], consistent with the causal distribution in [T19].
- **FTA** — model the causal chains leading to failure, decomposing top events into basic events. Every FTA in the system must state its **exposure period** and its **basic event probabilities**; a bare probability figure with no time window is not a valid output. (This is the specific defect in [A25].)
- **QRA** — quantify likelihood against consequence in human, environmental and financial terms, feeding from M11.
- **Bowtie** — preventive and mitigative barriers either side of the top event, with barrier health status wired to live monitoring.

**The Detection axis is the product thesis.** In an FMEA, Severity is largely fixed by consequence class and downstream exposure. Occurrence is reduced by engineering and operational change, which is slow and capital-intensive. **Detection is the axis this platform moves directly and immediately.** That gives a defensible, quantified value proposition: deploy the platform, re-score Detection, and report the RPN reduction per failure mode. This is the number to put in front of a mine's risk committee, an insurer, or a board — and unlike a generic "improved safety" claim, it is auditable.

**Living risk assessment, not a shelf document.** The recurring criticism of FMEA and QRA is that they are static and reactive [A25]. The platform's answer is to make risk scores **stateful**: Occurrence and Detection are re-scored automatically as monitoring evidence accumulates and as instruments go offline. A piezometer array failing takes the Detection rating down and the RPN up, which triggers M12 and M13 without waiting for the annual review. Every re-score is a ledger event under M14.

**Interfaces:** consumes M01 (facility characteristics, consequence class), M08 (instrument availability and uncertainty), M09 (failure mode taxonomy, live warning-sign state), M11 (consequence quantification). Produces the monitoring plan that M01 holds and the adequacy test that M14 runs.

---

### M18 — Model Governance & ML Assurance *(new in v0.2, cross-cutting)*

**Purpose.** Everything predictive in this system will eventually be challenged in a forum where being wrong is expensive. This module is the control that keeps the analytics defensible. It exists largely because [A25] demonstrates, by counter-example, exactly how ML in this domain goes wrong.

**Metric discipline — the non-negotiable.** Tailings dam failure is a rare event: order 1.2% annual probability across a large inventory [L19]. **Accuracy is a prohibited headline metric.** A constant "no failure" predictor scores ~98.8%. Mandatory reporting instead:

- Precision and recall at a stated operating point
- **False alarm rate per facility-year** — the metric that determines whether operators keep trusting the system
- Missed detection rate
- **Lead time distribution** — the only output with operational meaning. Reference points: inverse velocity gave ~2 days at Cadia [T19]; the same event analysed independently suggested closer to a month [L19]. A model that is accurate but late is worthless.
- Calibration quality (reliability diagram, Brier score). Ranked scores are not probabilities.

**Governance controls:**
- **A worked example of correct reporting.** Nasategay (2020), catalogued in [Z24], applied semantic segmentation with a UNet architecture and weighted cross-entropy loss to detect rills on tailings dams, reporting **precision 83.3%, recall 72.0%, F1 77.2%**. Note what is present and what is absent: no headline accuracy figure, an explicit weighting choice to handle class imbalance, and both error directions disclosed. That is the reporting standard this module enforces, and it stands in useful contrast to [A25]'s uninterpretable accuracy figures. Recall of 72% also sets a realistic expectation — roughly one rill in four missed on a mature, well-scoped vision task.
- **Model registry** — version, training data provenance and date range, class balance, validation results, competent-person approval, review and retirement dates. Any model change is a ledger event under M14.
- **Shadow mode before live.** New models run in parallel without alerting authority until they have demonstrated lead time and false alarm performance on the specific site. Champion/challenger thereafter.
- **Site transferability is not assumed.** Per P4 — a model trained at one facility, in one climate, on one construction method does not transfer without revalidation. This mirrors the threshold-calibration rule in M05 and the coherence seasonality in M04.
- **Physics-consistency constraint.** Model features must be traceable to the failure-mode taxonomy in M09. A correlation with no mechanistic pathway does not get to raise an alert.
- **Explainability is mandatory.** Every model-generated alert exposes contributing features and the underlying raw observation. A black-box alert cannot be defended in an audit or an inquiry, and cannot be acted on responsibly by an engineer of record.
- **Human-in-the-loop.** Model output is decision support. Authority to order stand-down, evacuation or notification remains with a named person under M12. No autonomous escalation on model output alone.
- **Data gap honesty.** Where training data is insufficient for a credible model — which will be the common case early on — the module reports that and defers to rules-based detection under M09. Publishing a poorly calibrated model is worse than publishing none.

**Interfaces:** governs M10 in full; governs any ML classifier in M05, M06 and M07; feeds evidence to M14. Pairs with M08 — M08 governs measurement uncertainty, M18 governs inference uncertainty.

---

### M19 — Adoption, Capacity Building & Programme Sustainability *(new in v0.3)*

**Purpose.** The delivery vehicle for P8. DAMSAT organised roughly half its eleven work packages around non-technical delivery — user engagement, monitoring and evaluation, sustainability, and knowledge sharing — and its lessons-learnt record indicates these were where the residual risk sat, not in the technology [D21]. v0.2 had no home for any of it.

**Functions:**

- **Capacity building as product, not courtesy.** DAMSAT ran a two-day course plus online sessions reaching 23 organisations and 50-plus people [D21]. This is load-bearing under P2: a regulator who cannot interpret an InSAR velocity map cannot act on one, so an uninterpretable output has not improved governance. Training materials, competency assessment and refresher cadence are deliverables with a price.
- **Institutional continuity.** Government staff turnover repeatedly reset DAMSAT's buy-in, with the note that senior role changes cluster at the start of the calendar year in Peru [D21]. Design for it: documented role-based handover packs, named deputies, onboarding that does not depend on the original champion, and a relationship map maintained as data rather than in someone's head.
- **Ownership and exit.** DAMSAT's frankest admission is that despite stakeholder interest it could not conclude pre-agreements for anyone to take ownership after the project, because agreements with public institutions are slow [D21]. HR Wallingford consequently continues to maintain the basic system. For a commercial venture the equivalent question is: who pays for year four, and is that committed before year one closes? Define the minimum-viable service that survives loss of the anchor customer.
- **Pilot design for trust.** DAMSAT found that testing confined to pilot sites made trust harder to establish and that involving stakeholders in pilot site selection would have improved matters [D21]. Let prospective users choose sites they already have opinions about — a site where the user can check the output against what they already know is worth several where they cannot.
- **Monitoring and evaluation of the platform itself.** DAMSAT ran baseline, midline and endline evaluations through an independent partner and reported against defined KPIs — including a 10% reduction in annual risk to life downstream, and counts of organisations capacitated and authorities actively accessing warnings [D21]. Adopt the structure and instrument it from day one; a platform that claims risk reduction without a baseline cannot substantiate the claim to an insurer or a DFI.
- **Advisory board.** DAMSAT convened an international advisory board including a major operator and regional tailings expertise [D21]. Cheap credibility, useful challenge, and a route into the segments in M15.

**Adjacent-market note.** DAMSAT's stated expansion path beyond tailings was other linear and slope infrastructure — road and rail cuttings, remote pipelines [D21]. The M02/M04/M08 stack is largely asset-agnostic. Worth holding as an option, not a distraction.

---

## 4. Cross-cutting concerns

| Concern | Requirement |
|---|---|
| Security | Role-based access control, tenant isolation, encryption at rest and in transit, evidence-ledger integrity protection |
| Data sovereignty | Local hosting where regulators require it |
| Resilience | Store-and-forward for intermittent field connectivity; the system must degrade to manual inputs without loss |
| Cost governance | Per-facility data acquisition cost tracking against tier; commercial SAR at ~US$1 200/km²/yr and GNSS at US$60–120k per installation [L19] make this material |
| Retention | Monitoring obligations effectively run in perpetuity for closed facilities [L19]; archival strategy must reflect that |

---

## 5. Open questions and gaps in the current source set

1. ~~**Beach length measurement**~~ — **closed in v0.3.** DAMSAT's Tailings Beach module measures minimum water-to-crest distance and beach area from Sentinel-2 [D21]. Quantified further in v0.4: beach slope 1–4%, beach length inverse of slope (ICOLD B181), and Jeong & Kim's FoS-linked critical beach distance. See M05.
2. ~~**Freeboard quantification**~~ — **structurally closed in v0.4.** ANCOLD's five-allowance decomposition via [Z24]. See M05. *Numeric criteria per consequence category remain open.*
2b. **ANCOLD primary text** — still required for the numeric criteria. Downgraded in priority: [Z24] indicates ICOLD B158 and B181 are the more detailed instruments.
2c. **Base rate reconciliation** — [L19] and [Z24] read Azam & Li (2010) incompatibly (annual vs cumulative 1.2%). Blocks any published QRA output. **Resolve first.**
3. **Phreatic surface inference** — the single most diagnostic geotechnical parameter, and none of the three papers offers a remote method. Likely stays instrumentation-dependent; need sources on piezometer network design and on any EO/geophysical proxies.
4. **Liquefaction susceptibility** — Santamarina, Torres-Cruz & Bachus (2019), cited in [O22], is the obvious next read.
5. **GISTM control mapping** — need the standard itself plus the Global Tailings Review outputs; Franks et al. (2021) on disclosure-revealed stability risks is cited in [O22] and worth pulling.
6. **South African regulatory instruments** — SANS 10286, MHSA regulations, DWS dam safety classification. Determines what "auditing" legally means in our primary market.
7. **Alert threshold derivation** — how amber/red TARP triggers are quantitatively set per site. Not covered.
8. **False positive economics** — no source yet on the operational cost of false alarms, which will drive threshold policy.
9. **UAV/photogrammetry integration** — listed as a monitoring method in [T19] but not developed.
10. **Ground-based InSAR / slope stability radar** — deployed at Cadia post-failure (GroundProbe); integration pattern needed.
11. **Base failure rates for QRA** — [L19] gives ~1.2% annual PoF from an 18,401-site inventory; we need a better-constrained modern figure, ideally segmented by construction method, storage method and lifecycle phase. Priority.
12. **FMEA severity and detection rating scales** — no source yet for domain-appropriate scoring rubrics. Without these, M17's RPN outputs are not comparable across facilities.
13. **Non-stationary design storm methodology** — how to update return periods for climate projections in a way a regulator will accept.

### Priority acquisitions

Standards and primary texts:
- **GISTM** full text (ICMM/UNEP/PRI, 2020) plus Global Tailings Review supporting material — required for M14 control mapping. **Now the single highest-value acquisition**, because Topic III is effectively our product specification.
- **South African instruments** — SANS 10286 (confirm whether revised since 1998), MHSA regulations, DWS dam safety classification.
- **CDA Dam Safety Guidelines** (2013 ed.) — most-used instrument in the industry per [Z24]; source of the closed-loop state machine.
- **ICOLD Bulletin 158** (2018) and **Bulletin 181** (2019) — the most detailed surveillance and tailings-design instruments identified. **MAC Tailings Guide and OMS Manual** are freely downloadable and should be pulled immediately.

Data sources to evaluate for M01 seeding and M17 base rates:
- **worldminetailingsfailures.org** and the **WISE Uranium Project** failure chronology (1960–present, with incident type, volume released and impact) [Z24].

Papers. New leads from [Z24] are marked ★:

| Source | Closes |
|---|---|
| **Azam & Li (2010)**, *Tailings Dam Failures: A Review of the Last One Hundred Years* | ★ Gap 2c — the base rate discrepancy. **Blocking; get this first.** |
| Piciullo et al. (2022), *A new look at the statistics of tailings dam failures*, Engineering Geology 303 | Gap 11 — modern base rates, likely Grade A |
| ★ **Clarkson & Williams (2020)**, *Critical Review of Tailings Dam Monitoring Best Practice*, Int. J. Mining, Reclamation & Environment 34:119–148 | The direct predecessor review and the source of M03b's inspection taxonomy. Probably the richest single monitoring-practice source available. |
| ★ **Lumbroso et al. (2021)**, *DAMSAT: An Eye in the Sky for Monitoring Tailings Dams*, Mine Water and Environment 40:113–127 | The **peer-reviewed** DAMSAT paper. Would upgrade the DAMSAT evidence base from Grade B to A. |
| ★ **Basson, Broekman & Jacobsz (2021)**, *TD-DAQ: A Low-Cost Data Acquisition System Monitoring the Unsaturated Pore Pressure Regime in Tailings Dams*, HardwareX 10 | **South African, low-cost, open-hardware, wireless, measuring negative pore pressure and moisture content.** Directly relevant to M03a and to gap 3 (phreatic surface). Possibly the most commercially useful lead in this batch. |
| ★ **Ouellet et al. (2022)**, *Advanced Monitoring of Tailings Dam Performance Using Seismic Noise and Stress Models*, Communications Earth & Environment 3:301 | Gap 3 — ambient noise interferometry via geophone array, with seismic velocity strongly associated with water level change. A candidate non-invasive saturation proxy. |
| ★ Lyu et al. (2019), *A Comprehensive Review on Reasons for Tailings Dam Failures Based on Case History*, Advances in Civil Engineering | Source of the failure-cause statistics; needed to verify the M09 reweighting |
| ★ Gama et al. (2019), *Advanced DInSAR on Dam Stability Monitoring: Germano (Mariana)*, RSASE 16 | M04 — SBAS vs PSI technique selection; reports SBAS best for deformation detection and makes the cost case for developing-country operators |
| ★ Duan et al. (2023), *Retrospective Monitoring of Slope Failure Using InSAR Time-Series*, Natural Hazards 117 | M10 — the ~1 month rainfall-to-deformation lag |
| ★ Dong et al. (2017), IEEE Access 5 — IoT + cloud pre-alarm system | M09/M10 — closest published analogue: three pre-alarm levels, safety factors and reliability scores from instrumentation |
| ★ Jeong & Kim (2020), *Optimal Tailings Beach Distance*, Mining, Metallurgy & Exploration 37 | M05/M17 — converting beach distance into an FoS-linked criterion |
| ★ Rauhala et al. (2017), *UAV Remote Sensing Surveillance in Sub-Arctic Conditions*, Remote Sensing 9 | Gap 9 — UAV survey design; stable-perimeter ground control technique |
| ★ Slingerland et al. (2018), *Identification and Quantification of Erosion on a Sand Tailings Dam* | M07 — erosion detection; reports digital stereo aerial photography superior for identification but limited for quantification |
| Santamarina, Torres-Cruz & Bachus (2019), *Why coal ash and tailings dam disasters occur*, Science 364 | Gap 4 — liquefaction |
| Franks et al. (2021), *Tailings facility disclosures reveal stability risks*, Scientific Reports 11:5353 | Disclosure regime and portfolio risk; pairs with the Church of England figures |
| Mwanza, Mashumba & Telukdarie (2024), Procedia CS 232 | M01 digital twin + M18. South African (UJ) — partnership as well as citation. |
| Wan et al. (2023), *Satellite–air–ground integrated multi-source EO... for tailings reservoir monitoring*, Land Degradation & Development 34(7) | Closest published analogue to this platform's fusion architecture |
| Hariri-Ardebili et al. (2023), Eng. Applications of AI 126 | M18 — state of the art and honest limitations |
| Cacciuttolo & Atencio (2023), Minerals 13(11):1445 | M01 storage-method variants |

---

## 6. Suggested build sequence

| Phase | Modules | Rationale |
|---|---|---|
| 1 | M01, **M17**, M03b, M12 (basic), M16 (basic) | Digital twin + structured risk assessment + digitised manual inspection + alerting. Deliverable and saleable with no satellite dependency. M17 moves into Phase 1 because you cannot design a monitoring plan without first knowing which failure modes matter — it is the specification for everything downstream. |
| 2 | M03a, M08, M13 | IoT telemetry and closed-loop actions. Fusion begins. |
| 3 | M02, M05, M07 | Optical EO first — cheapest, most interpretable, and pond/change detection gives visible wins. |
| 4 | M04, M09 | InSAR and the failure-mode engine. Highest technical lift. |
| 5 | M10, **M18**, M11 | Predictive and consequence modelling. M18 ships *with* the first model, never after it. |
| 6 | M14, M15 | Full audit and multi-stakeholder transparency — the moat, but it needs the evidence base underneath it to be real. |
| **All** | **M19** | Runs continuously from before Phase 1 to after Phase 6. It is not a phase. |

**Three things must be in place in Phase 1 even though their modules land later:**
- *M14's ledger primitives.* Retrofitting immutability is not possible.
- *M17's RPN baseline.* The pre-deployment Detection scores are the counterfactual against which the platform's value is measured. Capture them before anything is installed, or the ROI case is unprovable forever after.
- *M19's evaluation baseline.* Same logic, different currency. DAMSAT ran a formal baseline before any intervention and could therefore report against KPIs at endline [D21]. Without a baseline, "we reduced risk" is an assertion, and insurers and development banks do not buy assertions.

**One thing that must be settled before Phase 1, outside the build entirely:** the data-sharing and confidentiality framework in M15. It is the precondition for operator participation, it took DAMSAT the length of the project without resolution, and no amount of engineering routes around it.
