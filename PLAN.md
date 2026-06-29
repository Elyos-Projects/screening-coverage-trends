# PLAN — screening-coverage-trends

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

> **Binding cancer guardrails (this project may not ship anything that violates them):**
> (1) **Open-access / aggregate / de-identified data ONLY.** Controlled-access and identifiable
> patient data (registry micro-data, claims, biobanks, dbGaP/EGA) are **out of scope** and require
> authorized access + IRB we do not have. (2) **Verify each source's license** before any use; record
> provenance on **every** assertion and every chart. (3) **No medical advice.** Patient-facing content
> is **education only**, carries a persistent *"This is general information, not medical advice —
> consult your care team"* notice, and is **oncologist- and patient-advocate-reviewed** (`riskTier:
> high`, **blocking** sign-off before publish). (4) **Crisis / helpline information must be
> independently verified** (number, hours, region, language) before it is shown, and re-verified on a
> fixed cadence. These four rules lead the *Data, licensing & compliance* and *Quality, review & risk
> gates* sections below and are enforced in CI and review, not left to good intentions.

## Executive summary

Cancer screening saves lives by catching breast, cervical, colorectal, and (for high-risk groups)
lung cancers early — but **screening rates are uneven and trending in worrying directions** for many
populations: pandemic-era drops that have not fully recovered, persistent gaps by income, insurance,
race/ethnicity, rurality, and geography. The data describing these trends already exists in
**open, aggregate public sources** (US CDC BRFSS / PLACES / NHIS, NCI/CDC State Cancer Profiles, the
UK's OGL-licensed screening coverage releases, Eurostat, OECD, AIHW, Statistics Canada), but it is
scattered across portals, published in inconsistent definitions and denominators, wrapped in
statistical caveats most readers can't navigate, and rarely turned into something a non-specialist
can actually understand.

This project produces three tightly-scoped, **open** deliverables from that data: (1) a
**reproducible, source-verified open dataset** of harmonized cancer-screening-coverage **trends**
across indicators, geographies, and population breakdowns; (2) **reproducible analysis code** that
regenerates every number and chart from primary sources; and (3) **plain-language explainers** that
help the public, patients/advocates, journalists, and local public-health staff *correctly* read
those trends — what a screening rate is, what it is not, why a trend moved, and where to find
authoritative guidance.

The hard constraints are not technical; they are **legal, statistical, and clinical**. Legally, we
use only data whose license permits reuse and derivatives, and we treat non-commercial / share-alike
sources (WHO GHO is CC BY-NC-SA 3.0 IGO; IARC/GLOBOCAN is non-commercial) as **cite-only or excluded**
pending an explicit policy decision — never silently redistributed. Statistically, screening
estimates are easy to misread (self-report bias, model-based small-area estimates, changing guideline
denominators, suppressed small cells) and the explainers must *prevent* misreading, not add to it.
Clinically, **anything patient-facing is HIGH risk**: it is education, never advice, and ships only
after **oncologist + patient-advocate sign-off**. The plan front-loads a compliance/guardrail spec, a
per-source license gate, and a provenance-on-every-assertion rule before any content is produced.

The **deliverable is derived statistics and explanation, not patient data**. We never touch
individual-level records. "Delivered, not merged" means an open dataset + explainers that a named
beneficiary (a public-health program, a patient-advocacy org, a library/newsroom, or an OER channel)
actually adopts — not files in a repo. **No beneficiary partner and no expert reviewers are yet
secured**, so every task carries `verifiedNeed: false` until that changes, and the plan includes a
dated partner/reviewer-acquisition track with an explicit **build-vs-mothball decision rule** if no
beneficiary materializes.

## Problem & beneficiaries

**Who is helped.**
- **The public and patients/caregivers/advocates** — people trying to understand whether screening
  matters, whether rates are recovering near them, and where to find trustworthy guidance, without a
  statistics degree or a paywall.
- **Local public-health staff and community-health workers** — who need clean, cited, comparable
  trend data and plain explainers to target outreach, write grant narratives, and brief boards,
  but lack analyst time to harmonize raw federal/international releases.
- **Journalists and OER/educator channels** — who need accurate, attributable, reusable screening
  trend figures and explanations they can quote and teach from.
- **Open-data / cancer-stats reusers** — who benefit from a harmonized, provenance-rich derivative of
  otherwise-fragmented sources.

**The verified need.** The *general* need — declining/uneven screening coverage and a documentation
gap between raw statistical releases and public understanding — is well established by the source
agencies themselves (Healthy People screening targets, NCI Cancer Trends Progress Report, USPSTF and
ACS guideline rationales, post-2020 screening-recovery analyses). We treat the general need as real.
The **specific, last-mile need is TO BE SECURED**: we have **not** confirmed a named public-health
program, advocacy org, newsroom, or OER channel that has agreed to adopt these outputs. Until a
named beneficiary confirms they will review/accept/use a deliverable, every task carries
`verifiedNeed: false`. This honesty is load-bearing: the Elyos bar is *delivered*, not produced.

**Partner org.** TO BE SECURED. Candidate channels (M0 outreach, no partner assumed): state/local
public-health screening programs and CDC/NCI-funded coalitions; patient-advocacy organizations
(screening-focused); health/data desks at newsrooms; OER and data-literacy channels; open-data
portals. We also require **two distinct expert reviewers** — a **practicing/oncology clinician** and
a **patient advocate** — for HIGH-tier patient-facing content; both are **TO BE SECURED** and are a
blocking prerequisite for M3.

## Goals and non-goals

**Goals**
- Build a **reusable, reproducible pipeline** (source triage + license gate → aggregate-only
  acquisition → harmonization → trend analysis → provenance capture) that regenerates every published
  number and chart from primary open sources.
- Publish a **harmonized open dataset of screening-coverage trends** across the four screenable
  cancers (breast, cervical, colorectal, lung) with consistent definitions, denominators, time
  windows, geography, and population breakdowns — each value carrying full provenance and uncertainty.
- Make **license + provenance + privacy/small-cell verification a non-skippable, auditable gate**
  applied per source before any data is used.
- Produce **plain-language explainers** (education only) that help readers correctly interpret the
  trends and point them to authoritative guidance — oncologist- and advocate-reviewed, "not medical
  advice," with verified support/helpline pointers.
- Make every output **freely available** (open data CC-BY-4.0 or CC0 as the source requires; code
  MIT; explainers CC-BY-4.0) and accessible (WCAG 2.2 AA, plain-language readability target).
- Deliver outputs that a **named beneficiary actually adopts**, and track downstream outcomes.

**Non-goals**
- We do **not** use, request, host, or process **individual-level / controlled-access / identifiable**
  data (registry micro-data, claims, EHR/biobank, dbGaP/EGA). Aggregate/de-identified only.
- We do **not** give **medical advice**, personalized screening recommendations, eligibility rulings,
  or risk assessment. We explain *that* guidelines exist (USPSTF/ACS/national bodies) and cite them;
  we never tell an individual what to do.
- We do **not** diagnose, interpret an individual's results, or replace a clinician.
- We do **not** publish original incidence/mortality science or causal claims; we report **coverage
  trends** and clearly-attributed context, not new epidemiological conclusions.
- We do **not** redistribute or build derivatives from **non-commercial / no-derivatives** sources
  (e.g., WHO GHO CC BY-NC-SA, IARC/GLOBOCAN NC, Cancer Research UK all-rights-reserved) unless an
  explicit policy decision permits it; default is **cite-only or exclude**.
- We do **not** auto-publish; a human submits/contributes after review and expert sign-off.
- We do **not** rank, name-and-shame, or make politically-loaded claims from the data.

## Success metrics (outcomes)

Outcome-based and beneficiary-centric. Vanity metrics (charts produced, repo stars) are excluded.

| Metric | Baseline | Target (first ~9 months) |
| --- | --- | --- |
| Screening-trend deliverables **adopted/used by a named beneficiary** (last-mile delivered) | 0 | ≥ 3 adopted (e.g., a program cites it in outreach/board materials; a newsroom publishes from it; an OER channel teaches it) |
| Sources passing the **license + privacy/small-cell gate** before use / sources triaged | n/a | 100% of used sources have a recorded, verifiable license + gate artifact; 0 used sources with unresolved license/PII status |
| Patient-facing explainers shipped with **oncologist + advocate sign-off recorded** | 0 | 100% of shipped patient-facing explainers (no exceptions) |
| **Provenance coverage** — published assertions/charts with a resolvable source citation | n/a | 100% (a claim or chart without provenance fails CI; target 0 unsourced) |
| **Reproducibility** — published numbers regenerable from source by an independent run | n/a | 100% of dataset values reproduced by a clean CI run from cached source snapshots |
| Confirmed beneficiary partner(s) + expert reviewers secured | 0 | ≥ 1 beneficiary partner; ≥ 1 oncology clinician **and** ≥ 1 patient advocate secured |
| **Comprehension** — readers correctly interpret a trend explainer (small reader test / advocate panel) | n/a | ≥ 80% correct on the "what this rate does/doesn't say" check items |
| Factual/statistical defects found in expert review (per delivered explainer) | n/a | < 1 minor/expert revision per explainer; **0** uncorrected clinical-accuracy or "advice-creep" defects shipped |
| Verified helpline/support pointers (correct & current at publish) | n/a | 100% verified at publish; re-verified each release cycle |

Notes on attribution of outcomes: an "adoption" event must be **externally verifiable** (a citation,
a published article, a program's written confirmation, a course module using it). Self-reported use
does not count. Comprehension is measured against pre-registered check items, not vibes.

**Quantifying "harmonized" and "correctly interpreted" (so DoDs are checkable).**
- *Harmonization completeness* (0–100) per indicator×geography series: fraction of canonical fields
  populated and source-verified (definition, numerator/denominator, age band, period, source,
  uncertainty interval, suppression status). Target ≥ 90 for every published series.
- *Interpretation safety*: each explainer must pass a fixed **misread checklist** (does it state the
  denominator? distinguish self-report vs. measured? flag model-based estimates? avoid causal
  language? avoid advice? cite the guideline body?) — every item must pass before sign-off.

## Scope

**In scope**
- A **source catalog** of open, aggregate cancer-screening-coverage datasets with per-source license,
  provenance, definition, and privacy classification.
- A **reproducible pipeline** (TypeScript/ESM tooling + documented analysis steps) that fetches from
  primary sources, harmonizes to a canonical schema, computes trends with uncertainty, and emits the
  open dataset + figures with provenance.
- A **harmonized open trend dataset** (breast/cervical/colorectal/lung; US national + sub-national
  where licensed; selected international comparisons where licensed) with a data dictionary +
  Datasheet + machine-readable metadata.
- **Plain-language explainers** (education only) interpreting the trends and "how to read a screening
  statistic," with authoritative-guidance pointers, "not medical advice" framing, and verified
  support pointers — oncologist + advocate reviewed.
- **Static, accessible visualizations / a small dashboard** built only on the open dataset.
- License/provenance/small-cell **triage and recording** for every source and every breakdown.

**Out of scope**
- Any **individual-level / controlled-access / identifiable** data, in any form.
- **Medical advice**, personalized recommendations, eligibility/risk determinations, diagnosis.
- New **causal / epidemiological** conclusions; modeling beyond transparent, documented trend/CI math.
- Redistribution or derivatives of **NC / ND / all-rights-reserved** sources without an explicit
  policy decision (default exclude/cite-only).
- **Provider, clinic, or individual-level** ranking, naming, or targeting.
- **Automated, unattended publishing**; and anything that primarily serves a for-profit entity.
- **Live clinical decision support** of any kind.

## Solution approach & architecture

This is a **content/data project with light, reproducible software** — a pipeline that produces a
derived dataset + explainers, not a service that handles patient data.

**Pipeline (per source → per series → per explainer).**
1. **Source triage & gate** — identify dataset, publisher, license, aggregation level, and
   privacy/small-cell handling. PASS only if (a) license permits reuse + derivatives (or is cleared
   by the NC/SA policy), (b) data is genuinely aggregate/de-identified, and (c) suppression rules are
   understood. Record a committed **gate artifact**. Otherwise FLAG/EXCLUDE — never guess.
2. **Provenance capture** — source URL, publisher, retrieval timestamp, dataset version/vintage,
   survey/program name, license id + URL + snapshot (committed copy + SHA-256 + Wayback save),
   definition text, attribution string.
3. **Acquisition (aggregate only)** — fetch published aggregate tables/APIs; cache an immutable
   source snapshot. Never download individual-level extracts. Honor the access protocol below.
4. **Harmonization** — map each series to the canonical schema (indicator, population, geography,
   period, numerator/denominator definition, value, uncertainty, suppression flag, source ref).
5. **Trend analysis** — transparent, documented computation: trend over time, change vs. baseline,
   confidence/credible intervals propagated from source standard errors; **no smoothing or modeling
   that isn't documented and reproducible**. Model-based source estimates (e.g., PLACES) carried with
   their model caveats intact.
6. **Caveat layer** — attach machine-readable caveats to every series (self-report vs. measured;
   model-based small-area; denominator/guideline change; survey redesign break-in-series; small-cell
   suppression). Caveats render into both dataset metadata and explainers.
7. **Explainer authoring** — plain-language, education-only interpretation; pass the misread
   checklist; attach "not medical advice" + authoritative-guidance pointers + verified support
   pointers.
8. **Review & ship** — license/provenance reviewer + statistical reviewer for data; **oncologist +
   patient-advocate sign-off for any patient-facing content** (blocking); then a human publishes and
   the steward records adoption.

**Canonical trend-series model (single source of truth; all outputs are projections of it).**
`id`, `indicator` (breast|cervical|colorectal|lung), `measure` (e.g., "up-to-date with USPSTF
mammography"), `population {ageBand, sex, raceEthnicity?, income?, insurance?, urbanRural?}`,
`geography {level: nation|region|state|county|place, code, name}`, `period {year|range}`,
`value {pct, ciLow, ciHigh, basis: self-report|measured|model-based}`, `denominatorDef`,
`suppression {suppressed:boolean, rule}`, `source {id, url, version, retrievedAt, license, snapshotRef}`,
`caveats[]`, `provenanceComplete:boolean`, `harmonizationScore`.

**Tech stack.** TypeScript, ESM, pnpm workspaces (Elyos convention). Small, dependency-light Node
packages for fetch/cache, schema validation (the canonical model as JSON Schema), trend math, and
static-chart generation; analysis steps documented and reproducible in CI from cached snapshots.
Data authored as CSV/Parquet + JSON metadata; explainers as Markdown; dashboard as a static site.
**No runtime service, no database of people, no PII anywhere.**

**Dataset-access protocol (makes "aggregate-only, never store individual data" enforceable).**
- **Published aggregates only** — use agency aggregate tables/APIs (e.g., BRFSS prevalence tables,
  PLACES measure files, State Cancer Profiles exports, Eurostat/OECD/UK aggregate releases). Never
  request, download, or derive from micro-data/PUMS-style individual records, even where public.
- **Snapshot + checksum** — every fetch writes an immutable, checksummed snapshot with provenance;
  analysis runs against snapshots so results are reproducible and source-stable.
- **Small-cell guard** — never publish or re-derive a cell the source suppressed, and apply our own
  minimum-cell / minimum-denominator threshold; sub-group breakdowns that fall below threshold are
  suppressed and labeled, never imputed to a precise number.
- **No re-identification work** — no linkage, no disaggregation that could re-identify, ever.

**Key decisions.**
- **Canonical-model-first** so dataset, metadata, charts, and explainer figures never drift apart.
- **License/privacy gate is a blocking, committed artifact**, not an informal judgement.
- **Provenance-on-every-assertion enforced in CI** (a number or chart without a resolvable source ref
  fails the build).
- **Patient-facing = HIGH tier, expert sign-off blocking** — wired into the publish gate, not optional.
- **NC/SA sources default to cite-only/exclude** pending an explicit, recorded policy decision.

## Data, licensing & compliance

**THIS SECTION LEADS WITH THE BINDING CANCER GUARDRAILS (see header) and is the project's spine.**
Because we describe and redistribute derivatives of others' health statistics and produce
patient-facing health education, licensing, privacy, and provenance are treated conservatively.

**Guardrail 1 — Aggregate / de-identified / open-access ONLY.** Every source must be genuinely
aggregate or publisher-de-identified. Controlled-access, identifiable, or individual-level data
(registry micro-data, insurance claims, EHR, biobanks, dbGaP/EGA) is **out of scope** and excluded at
triage — we have neither authorized access nor IRB, and acquiring them is not a goal. SEER/GLOBOCAN
are aggregate (incidence/mortality context), not screening micro-data; even so, each is used only
within its license (see below).

**Guardrail 2 — Per-source license verification + provenance on every assertion.** No source is used
until its license is verified and recorded; no value or chart is published without a resolvable
citation to the exact source table/version. Provenance coverage is a CI-enforced 100% gate.

**Source license classification (conservative; verified per source at the gate).**

*Accepted — permits reuse + derivatives (attribution as noted):*
- **US federal works — public domain (CC0-like):** CDC **BRFSS**, CDC **PLACES** (model-based
  small-area estimates), CDC/NCHS **NHIS**, **NCI/CDC State Cancer Profiles**, **NCI Cancer Trends
  Progress Report**, **USPSTF** recommendations (as cited guidance), **SEER** aggregate stats (context
  only; not a screening source). No license bar to reuse; we still record full provenance.
- **UK Open Government Licence v3.0:** NHS England / UKHSA **cancer screening coverage** releases
  (breast, cervical, bowel). Accepted; attribution required.
- **Eurostat:** screening indicators — free reuse with attribution. Accepted; attribution required.
- **Statistics Canada Open Licence; AIHW (Australia) CC-BY (verify version):** accepted with
  attribution, license confirmed per dataset at the gate.
- **OECD Health Statistics / Health at a Glance:** many OECD datasets are CC-BY-4.0, **but terms vary
  by table** — classified **VERIFY**, accepted only once the specific table's CC-BY (or open) status
  is confirmed and recorded; otherwise cite-only.

*Restricted — non-commercial / share-alike / no-derivatives → default CITE-ONLY or EXCLUDE, pending
the written NC/SA policy decision (task `policy`/`sources`):*
- **WHO Global Health Observatory (cervical screening coverage):** **CC BY-NC-SA 3.0 IGO** — NC + SA.
  Conflicts with our CC-BY/open redistribution and "freely available for any reuse." Default
  **cite-only** (we may reference and link a WHO figure, but not redistribute a CC-BY derivative of
  it) unless the policy explicitly permits an NC-compatible carve-out.
- **IARC / GLOBOCAN / Cancer Over Time:** non-commercial terms — **cite-only** for context; not
  redistributed as an open derivative.
- **Cancer Research UK statistics, NCQA/HEDIS, OECD tables that prove non-open:** all-rights-reserved
  / proprietary → **excluded**, never "best-guessed" as open.

*Excluded outright:* any source with no clear license, "all rights reserved," no-derivatives terms
(unless cite-only suffices), or any individual-level/controlled data.

**Objective "permits derivatives" criterion.** A source PASSes for *redistribution* only if its
license is on the accepted list **and** an explicit `license.permitsDerivatives: true` is recorded
with the cited clause/URL. Missing evidence, NC/SA without a policy carve-out, or unparseable terms =
cite-only or EXCLUDE — never default-allow.

**Provenance model.** Every series records: source URL, publisher, retrieval timestamp, dataset
vintage/version, survey/program, license id + URL + **snapshot (committed copy + SHA-256 + Wayback
save)**, the exact definition/denominator text, and the attribution string. Every published number
and chart links back to its series provenance. Provenance is part of the committed deliverable and
CI-checked.

**Privacy / PII / small-cell stance.** We use only aggregate data and never handle PII. Residual
re-identification risk lives in **fine-grained sub-group breakdowns** (small geographies × small
demographic cells). Mitigations, applied at the gate and in the pipeline:
- Never publish or re-derive a cell the **source suppressed**; honor the source's suppression rules.
- Apply our **own minimum-cell/denominator threshold**; below threshold → suppress + label, never
  impute a precise value.
- **No linkage** across datasets that could re-identify; no disaggregation beyond the source's own.
- Treat model-based small-area estimates (PLACES) as **estimates with uncertainty**, never as counts
  of identifiable people.

**Clinical-content compliance (Guardrails 3 & 4).** Patient-facing content is **education only**,
`riskTier: high`, and ships only after **oncologist + patient-advocate sign-off (blocking)**. It
carries the persistent *"general information, not medical advice — consult your care team"* notice,
never gives personalized recommendations, attributes any guideline statement to its body
(USPSTF/ACS/national programs) with citation and version/date, and any **crisis/helpline/support
pointer is independently verified** (number, region, hours, language) before display and re-verified
each release. Guideline content carries a **valid-until / re-verify date**; past it, the claim is
flagged or withheld until re-checked and re-signed-off.

**Attribution & output licenses.** All outputs attribute source publishers per their licenses and
link to originals, and clearly state our contribution is the *derived statistics + explanation*, not
the source data. **Open dataset: CC-BY-4.0** (or **CC0** where a source/our policy prefers it, e.g.,
US-PD-only series); **code: MIT**; **explainers/docs: CC-BY-4.0**.

## Quality, review & risk gates

**Risk tier: medium overall; HIGH for all patient-facing content** (per the binding guardrails).
The pipeline/dataset surface is medium (factual/statistical/license accuracy). Any content a member
of the public reads about cancer screening is HIGH and gated on credentialed sign-off.

**Required review before a deed is "done" (none skippable):**
- **License + provenance reviewer** (every source & every published series): confirms verified
  license, recorded provenance, accepted-or-cite-only status, and 100% provenance coverage.
- **Statistical reviewer** (every dataset release & every figure): confirms definitions/denominators,
  uncertainty handling, suppression, break-in-series flags, and that no causal/over-claim language
  appears; CI green (reproducible from snapshots).
- **Oncology clinician reviewer (HIGH, blocking)** + **patient-advocate reviewer (HIGH, blocking):**
  every patient-facing explainer. Clinician confirms clinical accuracy and no advice-creep; advocate
  confirms comprehension, tone, and that it serves (not alarms) patients. Both sign-offs recorded in
  a reviewers ledger before publish. **No patient-facing content ships without both.**
- **Accessibility check:** explainers/dashboard meet WCAG 2.2 AA and the plain-language readability
  target before publish.

**CI-enforced gates (so "green" means something).**
- **Provenance coverage = 100%:** any value/chart lacking a resolvable source ref fails the build.
- **License-status gate:** any series whose source lacks a recorded accepted/cleared license fails.
- **Schema + reproducibility:** dataset validates against the canonical JSON Schema and regenerates
  byte-stably from cached snapshots.
- **Small-cell guard:** any published cell below the suppression threshold fails.
- **"Not-medical-advice" + misread-checklist lint:** patient-facing files must carry the notice and
  pass the automated misread-checklist lint (denominator stated, basis labeled, no advice verbs, no
  causal verbs on associational data, guideline body cited) before they are eligible for sign-off.
- **Helpline-verification gate:** any support/helpline pointer must reference a dated verification
  record.

**Definition of Shipped (project level).** A harmonized open dataset and/or explainer that is
**(a)** fully provenance-covered and reproducible, **(b)** license-cleared, **(c)** statistically
reviewed, **(d)** for patient-facing content, oncologist- **and** advocate-signed-off with the
"not medical advice" framing and verified support pointers, **(e)** accessible, and **(f) adopted/used
by a named beneficiary** with the adoption evidence recorded by the steward. Producing the artifact is
**not** shipped; recorded adoption by a real beneficiary is.

## Roadmap & milestones

Phased; each phase has measurable EXIT CRITERIA. M0 is a thin cold-start; later phases scale. The
HIGH-tier patient-facing phase (M3) is **hard-gated** on secured expert reviewers.

**M0 — Compliance spine, license gate & cold-start foundation (thin).**
- Goal: encode the binding guardrails, stand up the license/privacy gate + provenance model + repo,
  prove the gate on one source, begin partner/reviewer outreach. No patient-facing content yet.
- Exit criteria: (1) **cancer data & compliance guardrail spec** written and reviewed (the contract
  later code/content implements); (2) **source catalog v0** with per-source license classification +
  the **NC/SA policy decision** merged; (3) repo skeleton (TS/ESM/pnpm) + CI with provenance-coverage
  and license-status gates wired; (4) canonical trend-series schema + provenance model published;
  (5) the gate applied end-to-end to **one** accepted source (e.g., a US BRFSS/NHIS breast-screening
  national series) with a committed gate + provenance artifact; (6) ≥ 1 beneficiary-outreach thread
  and ≥ 1 expert-reviewer outreach thread opened.

**M1 — Reproducible pipeline + first trend dataset (one indicator, US).**
- Goal: end-to-end reproducible production of one indicator's trend with uncertainty + caveats.
- Exit criteria: (1) pipeline fetches→snapshots→harmonizes→computes trend+CI for **one indicator**
  (breast/mammography, US national + states) from accepted PD sources; (2) dataset validates against
  schema, regenerates from snapshots in CI, harmonization score ≥ 90; (3) caveat layer attaches
  self-report/break-in-series/suppression flags; (4) small-cell guard enforced; (5) statistical +
  license/provenance review passed; (6) Datasheet + data dictionary published.

**M2 — Multi-indicator, multi-geography trend dataset (data, no patient-facing).**
- Goal: scale to all four indicators and add licensed international comparisons + disparities-aware
  breakdowns (suppression-safe).
- Exit criteria: (1) breast, cervical, colorectal, lung trends for US national + sub-national;
  (2) ≥ 1 licensed international comparison series (UK OGL / Eurostat / OECD-verified / AIHW / StatCan)
  added with attribution; (3) disparities breakdowns (where licensed + above threshold) with
  suppression labels; (4) all series provenance-covered + reproducible; (5) WHO/IARC handled as
  cite-only per policy (no redistribution); (6) statistical + license review across the set.

**M3 — Plain-language explainers (HIGH, expert-gated). HARD-GATED ON SECURED EXPERTS.**
- Goal: education-only explainers that make the trends correctly readable, expert-signed-off.
- Entry gate: **oncology clinician AND patient-advocate reviewers secured** (else M3 does not start).
- Exit criteria: (1) "how to read a screening statistic" explainer + ≥ 2 indicator trend explainers,
  each passing the misread checklist; (2) "not medical advice" framing + authoritative-guidance
  pointers (USPSTF/ACS/national) cited with version/date; (3) **verified** support/helpline pointers;
  (4) **oncologist + advocate sign-off recorded** for each; (5) WCAG 2.2 AA + readability target met;
  (6) comprehension check ≥ 80% on misread items (advocate panel / small reader test).

**M4 — Accessible visualization / dashboard + data-literacy framing.**
- Goal: a static, accessible dashboard over the open dataset that doesn't mislead.
- Exit criteria: (1) static dashboard built only on the open dataset, every chart provenance-linked
  and uncertainty-shown; (2) WCAG 2.2 AA; (3) no chart implies causation or advice; (4) statistical +
  accessibility review passed.

**M5 — Delivery & handoff (the deed). GATED ON A SECURED BENEFICIARY.**
- Goal: a named beneficiary adopts/uses a deliverable; record the outcome.
- Exit criteria: (1) beneficiary secured (`verifiedNeed` flips true); (2) a deliverable adopted/used
  with externally-verifiable evidence recorded by the steward; (3) for any patient-facing piece used,
  expert sign-off + framing confirmed in the shipped form. **Build-vs-mothball rule:** if no
  beneficiary by **2027-03-31**, pivot the last-mile (hand the dataset+toolkit to an OER/open-data
  channel as a reference deed) or mothball to maintenance-only — recorded in governance — rather than
  ship to no beneficiary.

**M6 — Sustain, refresh & scale (post-delivery).**
- Goal: keep data current and content clinically valid; sustainable ownership.
- Exit criteria: (1) refresh runbook + annual data-release cadence tied to source vintages;
  (2) content re-review cadence (guideline re-verification + re-sign-off; helpline re-verification);
  (3) named maintenance rotation + steward; (4) documented, gated process for adding an indicator,
  geography, or language.

Dependencies: M1 needs M0's gate+schema; M2 builds on M1's pipeline; **M3 needs M2's data AND secured
experts**; M4 needs M2/M3 outputs; M5 needs a secured beneficiary + reviewed deliverables; M6 follows
delivery.

## Work breakdown

The itemized, schema-mapped backlog lives in `TASKS.md`, organized by the milestones above with a
task table (`ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer`), acceptance
criteria for the most important tasks per milestone, a milestone Definition of Done, a sized-but-
unscheduled backlog, and one complete, schema-valid example Task JSON for the first M0 task. The
**source catalog** (the funnel that feeds per-source/per-series tasks) is maintained in the repo and
referenced by the catalog-triage task; throughput from M2 onward is driven by triaging catalog
sources through the license/privacy gate into harmonized series.

## Governance, roles & stakeholders

- **Maintainer (Owner):** TBD — owns the pipeline, source catalog, schema, and backlog.
- **License + provenance reviewer:** TBD (TO BE SECURED) — mandatory, non-skippable gatekeeper for
  every source/series; can read open-data + health-statistics licenses (CC/OGL/IGO/OECD/SPDX) and
  apply the privacy/small-cell methodology. At least one named, qualified reviewer must exist or
  triage halts.
- **Statistical reviewer(s):** rotation of contributors who verify definitions, denominators,
  uncertainty, suppression, and reproducibility (CI green).
- **Oncology clinician reviewer (HIGH, blocking):** TBD (TO BE SECURED) — credentialed clinician;
  signs off clinical accuracy and no-advice-creep on every patient-facing piece.
- **Patient-advocate reviewer (HIGH, blocking):** TBD (TO BE SECURED) — signs off comprehension,
  tone, and patient-serving framing on every patient-facing piece.
- **Steward (last-mile owner):** TBD — owns beneficiary relationships and records adoption (the
  "delivered" signal); owns helpline re-verification cadence.
- **Partner / requestor / beneficiary:** TO BE SECURED — named public-health program, advocacy org,
  newsroom, or OER channel.

Two distinct HIGH-tier reviewers (clinician + advocate) are required precisely because clinical
accuracy and patient-serving comprehension are different competencies; one cannot substitute for the
other. Until both are named, all patient-facing tasks stay blocked and `verifiedNeed: false`.

## Dependencies & integrations

- **Open data sources (per-source license verified at gate):** CDC BRFSS, CDC PLACES, CDC/NCHS NHIS,
  NCI/CDC State Cancer Profiles, NCI Cancer Trends Progress Report (US PD); NHS England/UKHSA
  screening coverage (UK OGL v3); Eurostat; OECD Health Statistics (verify per table); AIHW; Statistics
  Canada. **Cite-only/restricted:** WHO GHO (CC BY-NC-SA 3.0 IGO), IARC/GLOBOCAN (NC). **Excluded:**
  Cancer Research UK, NCQA/HEDIS, any controlled/individual-level data.
- **Guidance bodies (cited, versioned):** USPSTF, American Cancer Society, and national screening
  programs — referenced as authoritative guidance, never restated as our advice.
- **Standards/specs:** SPDX license ids, schema.org/Dataset + Datasheets-for-Datasets + (optionally)
  Croissant ML for dataset metadata; WCAG 2.2 AA; a plain-language readability standard.
- **Elyos pieces:** Task JSON schema (`packages/schema`), the donated-lane CLI workspace/PR flow
  (`packages/cli`), good-deed definition + refusal guardrails. Donated lane only — no funded-lane/
  runner dependency, no API-key execution.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| Patient-facing content drifts into medical advice | Medium | High | HIGH tier; misread-checklist lint; "not medical advice" framing; **blocking oncologist + advocate sign-off**; refuse personalized/eligibility framings | Clinician + Advocate reviewers |
| Misreading a screening statistic (self-report vs. measured, model-based, denominator change) | High | High | Mandatory caveat layer per series; statistical review; explainer misread checklist; show uncertainty | Statistical reviewer |
| Using a non-open/NC source as if open (e.g., WHO GHO, GLOBOCAN, CRUK) | Medium | High | Per-source license gate; NC/SA policy decided up front; default cite-only/exclude; recorded license + clause | License reviewer |
| Re-identification via fine-grained sub-group × small-geography cells | Low | High | Honor source suppression; own min-cell/denominator threshold; suppress+label, never impute; no linkage | License reviewer + Maintainer |
| Accidentally touching individual-level/controlled data | Low | High | Aggregate-only access protocol; out-of-scope at triage; reviewers reject any micro-data task | Maintainer |
| Outdated guideline/helpline info shown as current | Medium | High | valid-until/re-verify dates; helpline-verification gate; re-review cadence (M6) | Steward + Clinician reviewer |
| No beneficiary secured → outputs produced but not adopted | Medium | High | M0 outreach; steward role; `verifiedNeed:false` until secured; build-vs-mothball rule (2027-03-31) | Steward |
| No expert reviewers secured → M3 cannot ship | Medium | High | Reviewer outreach in M0; M3 hard-gated; data milestones (M1/M2) deliver value without M3 | Maintainer + Steward |
| Source release changes definitions / breaks series | Medium | Medium | Snapshot + version capture; break-in-series flags; reproducible from snapshots; refresh runbook | Maintainer |
| Causal/over-claim language from associational data | Medium | Medium | No-causal-verb lint; statistical review; explicit "trends, not causes" framing | Statistical reviewer |
| Politicized misuse of disparity figures | Medium | Medium | Neutral framing; context + caveats; no ranking/name-and-shame; non-partisan guardrail | Maintainer |

## Security & privacy

- **Threat surface is small** (no runtime service handling people, no individual data, no PII store).
  Main surfaces are CI, the cached source snapshots, and the published dataset/explainers/dashboard.
- **Secrets handling:** the pipeline needs no credentials by default (public APIs/files). Any API
  token (rare) is supplied by the human running it and is never written to logs, receipts, snapshots,
  or committed files (per Elyos rules).
- **PII:** none handled. The only residual privacy risk is re-identification via over-fine
  breakdowns — controlled by suppression + threshold + no-linkage rules above.
- **Abuse/misuse prevention:** refuse and flag any task steering this toward individual/provider
  targeting, de-anonymization, disaggregation below safe thresholds, medical-advice generation,
  alarmist/deceptive framing, or laundering an NC/closed source as open. Outputs stay descriptive,
  educational, source-verified, and non-partisan.

## Sustainability & maintenance

- **Post-delivery ownership:** the steward owns beneficiary relationships, adoption tracking, and
  helpline re-verification; the maintainer keeps the pipeline, schema, and source catalog current
  with source-vintage and spec changes.
- **Refresh cadence:** annual data releases aligned to source vintages (BRFSS/NHIS/PLACES/Eurostat/
  OECD update cycles); each release re-runs the pipeline from fresh snapshots, re-checks licenses, and
  bumps versions. Stale series are flagged via recorded vintage + cadence.
- **Content validity:** guideline statements carry valid-until dates and are re-verified +
  re-signed-off on cadence; helpline/support pointers re-verified each release.
- **Outcome tracking:** the steward records adoption + external reuse events against the success
  metrics; reviewed each milestone and each annual release.

## Open questions

- Which named beneficiary (public-health program / advocacy org / newsroom / OER channel) will be the
  first confirmed adopter — and which deliverable do they most need first?
- Who are the credentialed **oncology clinician** and **patient-advocate** reviewers (M3 blocker)?
- **NC/SA policy:** do we ever produce an NC-compatible derivative of WHO GHO / IARC, or strictly
  cite-only? (Default: cite-only/exclude until decided and recorded.)
- Which single indicator+geography is the M1 pilot (proposed: US mammography/breast, BRFSS+NHIS)?
- CC-BY-4.0 vs. CC0 for the open dataset where all underlying sources are US public domain?
- Scope of international comparison in v1 (which licensed countries beyond the US)?
- Languages for explainers in v1, and whether translation becomes a sibling project (per-language
  review needed)?
- Do we adopt Croissant ML metadata now or defer to a later release?

## References

- Elyos work rules — `C:\code\elyos\CLAUDE.md`
- Good Deed Definition + risk tiers — `C:\code\elyos\docs\good-deed-definition.md`
- Task JSON schema — `C:\code\elyos\packages\schema\src\schemas.ts`
- Portfolio roadmap (cancer track guardrails) — `C:\code\elyos\planning\ROADMAP.md`
- Sibling cancer/data plans (format precedent) — `planning/projects/open-data-datasheets/PLAN.md`,
  `planning/projects/public-official-guide/{PLAN,TASKS}.md`
- Proposal — `governance/proposals/screening-coverage-trends.md` (TO BE WRITTEN)
- Data sources — CDC BRFSS, CDC PLACES, CDC/NCHS NHIS, NCI/CDC State Cancer Profiles, NCI Cancer
  Trends Progress Report, NHS England/UKHSA screening coverage (OGL v3), Eurostat, OECD Health
  Statistics, AIHW, Statistics Canada; WHO GHO (CC BY-NC-SA 3.0 IGO, cite-only), IARC/GLOBOCAN (NC,
  cite-only)
- Guidance bodies — USPSTF, American Cancer Society, national screening programs
- Standards — SPDX, schema.org/Dataset, Datasheets for Datasets (Gebru et al.), Croissant ML,
  WCAG 2.2 AA

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified during drafting and are **already applied** in
the plan above (and in `TASKS.md`). Each lists the change and where it landed.

1. **Two-tier risk model made explicit** — separated the medium-risk data/pipeline surface from the
   HIGH-risk patient-facing surface, with different gates for each. *(Quality gates; Risk table.)*
2. **Binding guardrails hoisted to a header block** so they govern the whole document and lead the two
   required sections, not buried mid-plan. *(Header; Data/licensing; Quality gates.)*
3. **Per-source license classification table** with named sources sorted into accepted / cite-only /
   excluded, instead of a vague "verify licenses." *(Data, licensing & compliance.)*
4. **WHO GHO NC-SA + IARC/GLOBOCAN NC explicitly flagged** as cite-only/exclude with a required NC/SA
   policy decision merged in M0 — closing the most likely license trap. *(Data; M0; Risks.)*
5. **Provenance-on-every-assertion turned into a CI gate** (100% coverage or build fails), not a
   principle. *(Quality gates; Success metrics.)*
6. **Self-report vs. measured vs. model-based "basis" field** added to the canonical model and a
   mandatory caveat, since misreading these is the top statistical risk. *(Architecture; Risks.)*
7. **Model-based small-area estimates (PLACES) caveat** carried intact rather than presented as counts.
   *(Architecture; Data.)*
8. **Small-cell / re-identification guard** (honor source suppression + own min-cell threshold +
   no-imputation + no-linkage) added as a concrete privacy control for an aggregate-only project.
   *(Architecture; Data; Security.)*
9. **"Not medical advice" elevated to an automated lint + framing requirement** wired into the publish
   gate. *(Quality gates.)*
10. **Misread checklist** defined as a checkable, pre-registered gate for every explainer (denominator,
    basis, model-based flag, no causal/advice verbs, guideline citation). *(Success metrics; Quality.)*
11. **Two distinct HIGH-tier reviewers required** (oncology clinician + patient advocate) with the
    rationale that accuracy ≠ comprehension; one cannot substitute. *(Governance; Quality gates.)*
12. **M3 hard-gated on secured experts** so patient-facing work literally cannot start without sign-off
    capacity. *(Roadmap M3.)*
13. **Crisis/helpline verification gate** (verified at publish + re-verified each release, with a dated
    record) — addressing the binding "helpline must be verified" rule. *(Data; Quality; Sustain.)*
14. **Guideline valid-until / re-verify dates** so guidance can't silently go stale. *(Data; Sustain.)*
15. **Reproducibility-from-snapshots** made a success metric and CI gate (every value regenerable),
    raising the bar beyond "we ran it once." *(Architecture; Success metrics; Quality.)*
16. **Immutable snapshot + SHA-256 + Wayback** provenance for both data and license text, borrowed from
    the datasheets project's proven pattern. *(Architecture; Data.)*
17. **Canonical-model-first** so dataset / metadata / charts / explainer figures never drift. *(Arch.)*
18. **Aggregate-only access protocol** written as an enforceable rule (published aggregates only; never
    micro-data even where public). *(Architecture; Data; Non-goals.)*
19. **Build-vs-mothball decision rule with a date (2027-03-31)** so the project can't drift into
    producing outputs no one adopts. *(Roadmap M5; Risks.)*
20. **Outcome-based success metrics** (adoption, comprehension, defects, reproducibility) replacing
    vanity metrics, with externally-verifiable adoption evidence. *(Success metrics.)*
21. **Disparities framing guarded** against ranking / name-and-shame / politicization (neutral,
    context+caveats, non-partisan). *(Scope; Risks.)*
22. **Honest verifiedNeed=false everywhere** + dated partner/reviewer-acquisition track, matching the
    "delivered, not merged" bar. *(Problem; Governance; TASKS.)*
23. **No-causal-verb lint** added so associational trend data isn't dressed as causation. *(Quality;
    Risks.)*
24. **Data milestones (M1/M2) deliver standalone value** even if experts/beneficiary lag, de-risking
    the dependency on hard-to-secure reviewers. *(Roadmap; Risks.)*
25. **License-status + provenance-coverage CI gates listed as concrete build-breakers**, plus a
    sandbox-grade reproducibility run, so "CI green" is meaningful for a data project. *(Quality.)*

---

## Review sign-off

**Reviewer:** drafting agent (senior staff engineer + TPM), self-review pass · 2026-06-28.

**Completeness check.** All 17 required H2 sections present and in spec order; metadata header
present; the two designated sections lead with the binding cancer guardrails; Appendix A with 25
applied improvements present; Risks table uses the required columns; roadmap phases each carry
measurable exit criteria; `TASKS.md` exists with schema-mapped tasks, per-milestone tables,
acceptance criteria, a backlog, and a schema-valid example Task JSON.

**Correctness check & fixes applied during review.**
- Verified the example Task JSON against `packages/schema/src/schemas.ts`: all 16 required fields
  present; enums valid (`type`, `lane`, `priority`, `riskTier`, `deliverable`, `tokenEstimate`,
  `status`); `acceptanceCriteria` non-empty; `output` non-empty; donated lane so no `fundedBudgetUsd`
  required. **Pass.**
- Confirmed no task uses the `funded` lane (so the funded-budget conditional never triggers);
  confirmed no individual-level/controlled data appears in any source list (guardrail 1).
- Confirmed every patient-facing task is `riskTier: high` with both clinician + advocate reviewers and
  blocking sign-off; corrected M3's entry gate to be explicit that it cannot start without secured
  experts.
- Confirmed NC/SA sources (WHO GHO, IARC/GLOBOCAN) appear only as cite-only/excluded and never in the
  redistributed dataset; confirmed CRUK / NCQA-HEDIS excluded.
- Confirmed `verifiedNeed: false` across all tasks (no beneficiary/expert secured) and that the
  build-vs-mothball rule + dated acquisition track are present.

**Open items requiring a human decision** (also in Open questions): secure a named beneficiary; secure
the oncology-clinician + patient-advocate reviewers; ratify the NC/SA license policy; choose dataset
license (CC-BY vs. CC0 for US-PD-only series). **Verdict: ready for maintainer review; no blocking
internal inconsistencies found.**
