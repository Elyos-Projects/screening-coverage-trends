# Competitive & Improvement Analysis — `screening-coverage-trends`

> Scope: cancer-screening-coverage **trends** (breast/cervical/colorectal/lung) from open, aggregate
> public data (BRFSS, PLACES, NHIS, State Cancer Profiles, OECD, Eurostat, WHO/IARC, UK OGL), with
> plain-language explainers. Medium-risk overall; HIGH-risk for any patient-facing content.
> Date: 2026-06-29. Sources cited inline as URLs.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong on governance: binding guardrails are hoisted to a header, the
license/provenance/small-cell gate is CI-enforced, NC/SA traps (WHO GHO, IARC/GLOBOCAN, CRUK) are
pre-classified, and the "delivered, not merged" bar with `verifiedNeed:false` is honest. The
canonical trend-series model already carries a `basis: self-report|measured|model-based` field and a
machine-readable caveat layer. That said, several substantive correctness gaps remain — most of them
statistical, and most of them the exact places where a screening-trend product misleads.

**1a. BRFSS self-report overestimation is under-specified as a *directional, differential* bias.**
The plan flags "self-report vs. measured" as a caveat *type*, but does not encode that the bias is
**systematic, upward, and non-uniform across subgroups**. Validation work is blunt: in one
record-linkage study 73% of women self-reported a mammogram in 2 years but only 44% were documented,
and over-reporting appeared in all ethnic groups
(https://www.ncbi.nlm.nih.gov/books/NBK539386/ ; the meta-analysis at
https://www.researchgate.net/publication/340203094). Because over-reporting can **mask disparities**
and **overstate program success** (https://pmc.ncbi.nlm.nih.gov/articles/PMC10890357/), the caveat
must be *directional* ("true coverage is likely lower; gaps may be larger than shown") and the
explainer misread-checklist should forbid comparing a self-report series against a
measured/administrative series (e.g., BRFSS vs. NHS England programme coverage) without a break
flag. **Recommendation:** add `biasDirection` and `comparableTo[]` (which other series this one may
be trended/compared against) to the canonical model; today nothing stops a chart juxtaposing
self-reported US rates with measured UK programme rates.

**1b. Guideline-change confounding is named but not operationalized as a structural break.** The plan
mentions "denominator/guideline change" caveats, but the trend math (step 5) propagates CIs without
a first-class **break-in-series / regime-change marker tied to a guideline event**. This is the
single largest threat to *trend* validity here: USPSTF moved colorectal start age 50→45 in May 2021
with near-immediate uptake in the 45–49 group
(https://jamanetwork.com/journals/jamanetworkopen/fullarticle/2824357), and moved breast screening
to start at 40 in 2024 (https://www.breastcancer.org/news/new-screening-guidelines-USPSTF). A naive
"up-to-date" trend line that spans these dates conflates *behavior change* with *denominator/eligibility
redefinition*. **Recommendation:** a `guidelineRegime` dimension on every series with explicit
break years; trends must not be drawn across a break without a visible discontinuity and an
explainer note. The plan's "trends, not causes" framing is necessary but not sufficient.

**1c. Age-eligibility denominators need to be a typed, first-class field, not prose.** "Up-to-date"
is meaningless without the eligible-age band, and bands differ by source and by guideline vintage
(OECD uses mammography 50–69/2yr, cervical 20–69/3yr, colorectal 50–69/2yr —
https://www.oecd.org/en/publications/2025/.../cancer-screening_a3f047dd.html ; County Health
Rankings' mammography measure is **only women 65–74 on Medicare Part B, past 1 year**, which
mismatches the every-2-year guideline and excludes everyone younger and uninsured —
https://www.countyhealthrankings.org/health-data/community-conditions/health-infrastructure/clinical-care/mammography-screening).
The model has `population.ageBand` and `denominatorDef` (good) but the harmonization spec should
**reject** cross-source trending where age bands or look-back windows differ, rather than leave it to
reviewer vigilance.

**1d. Jurisdictional guideline differences are acknowledged but the comparison hazard is deeper than
"note differences."** US (USPSTF/ACS), UK (NHS programme ages/intervals), EU (Council Recommendation),
and Australia/Canada all use different eligible ages, intervals, and *measurement methods* (population
survey vs. programme registry). An international "comparison" chart is the most likely place this
project ships something subtly wrong. **Recommendation:** treat international comparison as
**measurement-method-segregated** — never put survey-based self-report (US BRFSS, OECD survey rows)
on the same axis as programme-registry coverage (UK/EU programme data) without an explicit,
loud method label and ideally separate panels.

**1e. No-medical-advice boundary is well-drawn but has one soft edge: "where to find authoritative
guidance."** Pointing readers to USPSTF/ACS is fine; the risk is that *summarizing the differences*
between guideline bodies (USPSTF age 40 vs. ACS age 45 for breast) slides from "facts about
guidelines" into "implicit recommendation." The plan's misread-checklist should add an explicit item:
*guideline statements are attributed and dated, presented as plurality/disagreement where bodies
differ, and never resolved into a single suggested action.*

**1f. CIs/uncertainty: the plan propagates source SEs but doesn't address two specific issues.**
(i) PLACES are **model-based small-area estimates** with model uncertainty that is *not* sampling
error and should never be shown with a survey-style CI — the plan carries the model caveat but should
forbid mixing PLACES credible intervals and BRFSS sampling CIs in one comparison. (ii) **Multiple
comparisons / apparent-trend significance**: with many subgroup×geography series, some "trends" will
be noise; the plan has no stated rule for when a change is reportable vs. within-CI. **Recommendation:**
a documented minimum-detectable-change / overlapping-CI rule before a trend is described as a change.

**1g. Data currency is handled (vintage capture, valid-until dates, annual refresh) — good.** Minor
gap: BRFSS had a **2011 methodology/weighting change (raking + cell-phone inclusion)** that creates a
hard break-in-series for pre/post-2011 comparisons; the plan should hard-code that known break, plus
the NHIS questionnaire redesigns (2019), as built-in regime markers rather than discovering them
per-series.

**1h. Accessibility (WCAG 2.2 AA + plain-language target) is specified.** Strengthen by: requiring
charts to be non-color-dependent (colorblind-safe), uncertainty shown visually not just numerically,
and a stated readability metric/grade target (the plan says "a plain-language readability standard"
but doesn't pick one — choose, e.g., a grade-8 target, so it's testable).

**Completeness:** all 17 H2 sections present; metadata, guardrails, risk table, milestone exit
criteria, Appendix A all present. The biggest *missing* design artifact is a **comparability matrix**
(which series may be trended or compared against which) — currently comparability is implicit and
left to reviewers, which is the riskiest place to rely on humans.

---

## 2. Competitive landscape

**CDC PLACES** — model-based small-area estimates of cancer-screening test use down to county, place,
tract, and ZCTA (https://www.cdc.gov/united-states-cancer-statistics/about/tools.html ; PLACES).
*Strengths:* unmatched geographic granularity; authoritative; public-domain. *Weaknesses:* a single
cross-section per release (weak on *trends*); model-based estimates are easily misread as counts;
no plain-language interpretation; not framed for patients/journalists; modeled CIs are subtle.

**CDC BRFSS / U.S. Cancer Statistics Data Viz** — the underlying survey + a viz tool covering
screening-test use and risk factors (https://www.cdc.gov/united-states-cancer-statistics/dataviz/index.html ;
https://www.cdc.gov/pcd/issues/2018/17_0465.htm). *Strengths:* long time series, the canonical US
screening source, public-domain. *Weaknesses:* self-report over-reporting bias baked in and
not surfaced to lay users; weighting/2011 break not explained; analyst-oriented UI.

**NCI/CDC State Cancer Profiles** — county/state screening, incidence, mortality from BRFSS, with
maps/tables/graphs (https://statecancerprofiles.cancer.gov/risk/ ; overview
https://sparkmap.org/data-dive-state-cancer-profiles/). *Strengths:* combines screening with
outcomes; sub-state; trusted. *Weaknesses:* expert tool, dense; limited plain-language; trend
treatment thin; no guideline-change context.

**NCI Cancer Trends Progress Report** — narrative national trend reporting against Healthy People
targets (context for the established need; 2021 up-to-date rates 75.7% breast / 75.2% cervical /
72.2% colorectal, https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10625435/). *Strengths:* authoritative
trend narrative + targets. *Weaknesses:* national only, not granular; periodic static report; not
reusable open dataset; no sub-population deep dives.

**OECD Health at a Glance / Health Statistics** — cross-country breast/cervical/colorectal screening
coverage with harmonized age bands (https://www.oecd.org/en/publications/2025/11/health-at-a-glance-2025_a894f72e/full-report/cancer-screening_a3f047dd.html).
*Strengths:* international comparability, defined denominators, well-documented survey-vs-programme
distinction. *Weaknesses:* country-level only; **license varies by table (the plan's "VERIFY"
classification is correct)**; mixes survey and programme data; not patient-facing.

**WHO GHO / IARC (GLOBOCAN, CanScreen5)** — WHO cervical coverage (CC BY-NC-SA 3.0 IGO) and IARC's
CanScreen5 global programme repository (https://canscreen5.iarc.fr/ ;
https://www.nature.com/articles/s41591-023-02315-6). *Strengths:* global breadth; harmonized
indicators; CanScreen5 is the closest "harmonized screening repository" peer. *Weaknesses:* **NC/SA
or unspecified licensing blocks open redistribution** (the plan's cite-only/exclude stance is right);
programme-data, not US-style survey trends; little lay explanation.

**Our World in Data** — excellent reusable charts/explainers, but cancer **screening** coverage is
sparse and largely Europe-only; OWID's cancer focus is incidence/mortality
(https://ourworldindata.org/grapher/cancer-death-rates-by-type-who-db). *Strengths:* gold-standard
plain-language + reusable CC-BY graphers; the model to emulate. *Weaknesses:* thin/absent on
screening-coverage trends, especially US sub-national and disparities.

**Cancer Research UK** — authoritative UK screening stats and trends (cervical coverage 74.2%→68.8%
over the decade; breast 71.1%→64.6% post-COVID:
https://news.cancerresearchuk.org/2024/12/19/90000-cancer-cases-caught-by-screening-over-last-five-years/).
*Strengths:* clear public communication. *Weaknesses:* **all-rights-reserved (the plan correctly
excludes it)**; UK-only; not an open dataset.

**County Health Rankings & Roadmaps** — county mammography measure + action framing
(https://www.countyhealthrankings.org/health-data/community-conditions/health-infrastructure/clinical-care/mammography-screening).
*Strengths:* county coverage, action-oriented for local users (a close beneficiary-audience peer).
*Weaknesses:* mammography measure is **Medicare 65–74, past-1-year only** — a narrow, easily-misread
denominator; single measure; not multi-cancer trend.

**Net read:** authoritative *data* tools (PLACES, State Cancer Profiles, OECD) and excellent
*explanation* outlets (OWID, CRUK) exist, but **almost no one combines harmonized, provenance-rich,
multi-source screening-*trend* data with rigorous lay explanation, disparity-aware breakdowns, and
guideline-change/self-report caveats baked into the data itself.** That seam is the opening.

---

## 3. Gaps we can fill

1. **Harmonized cross-source *trend* layer.** Existing tools are single-cross-section (PLACES) or
   single-source (BRFSS, OECD). No one publishes one harmonized, reproducible, provenance-rich
   trend dataset spanning US national + sub-national + selected licensed international.
2. **Caveats encoded *in the data*, not just prose.** A machine-readable `basis`, `biasDirection`,
   `guidelineRegime`, break flags, and a comparability matrix is a genuinely novel artifact.
3. **Plain-language explanation that prevents misreading** (self-report ≠ measured; model-based ≠
   count; eligibility-band change ≠ behavior change). OWID does lay clarity; almost no one does it
   *for screening trends specifically* with disparity awareness.
4. **Disparity-aware, suppression-safe breakdowns** that neither hide gaps nor risk re-identification.
5. **Guideline-change context as data.** Tie each break to the USPSTF/ACS/NHS event that caused it —
   nobody offers this as a reusable, dated layer.
6. **Provenance/reproducibility to dataset-citation standard** (snapshot + SHA-256 + Wayback +
   Datasheet) — beyond what most government portals expose for *derived* numbers.
7. **Embeddable, accessible, CC-BY outputs** that a newsroom/library/OER channel can actually reuse,
   which the dense government tools don't provide.

---

## 4. Differentiators to win

- **The comparability/caveat engine is the moat:** screening trends are a minefield, and a product
  whose *data model itself* refuses unsafe comparisons (self-report vs. measured, across guideline
  breaks, mixing PLACES models with BRFSS samples) is more trustworthy than any portal that leaves
  this to the reader. **This is the single strongest differentiator.**
- **Provenance-on-every-number + byte-stable reproducibility** — citation-grade trust for journalists
  and researchers.
- **Two-audience design:** analyst-grade open dataset *and* oncologist/advocate-reviewed lay
  explainers from the same canonical source — neither government portals nor OWID do both for
  screening trends.
- **Disparity-aware but non-partisan, suppression-safe** framing — fills the County Health Rankings
  action-audience need without their narrow Medicare denominator.
- **Open licenses + embeddable widgets** so beneficiaries reuse rather than screenshot.

---

## 5. Claude API leverage (and hard limits)

**Where Claude adds leverage (donated lane; human-run, reviewed):**
1. **Plain-language explainer drafting *from computed statistics*** — Claude turns a finished,
   code-computed series (value, CI, basis, break flags) into a grade-8 explainer that states the
   denominator, labels self-report vs. measured, flags model-based estimates, and avoids causal/advice
   verbs. Stats come from code; Claude only narrates them.
2. **Per-region / per-audience scaling** — adapt one reviewed template across 50 states / many
   localities / multiple reading levels and (later) languages, holding the misread-checklist invariant,
   so expert review scales to a *template* not N bespoke texts.
3. **Guideline-context drafting** — assemble dated, attributed summaries of *what each guideline body
   says and how they differ* (USPSTF vs. ACS vs. NHS), as facts-with-citations for human verification —
   never resolved into a recommendation.
4. **Caveat-layer authoring assistance** — propose machine-readable caveats per series from source
   metadata; pre-screen drafts against the misread-checklist/no-causal-verb lint before human review.
5. **License/provenance triage assist** — draft per-source license classifications and SPDX mappings
   for the human license reviewer to verify (never auto-accept).

**Where Claude must NOT decide (hard guardrails):**
- **No screening or medical recommendations, eligibility rulings, or individualized advice** — ever.
  Screening decisions are individual, age-specific, and guideline-dependent; out of scope by design.
- **Statistics must come from reproducible code, not the model.** Claude never computes, estimates,
  imputes a suppressed cell, or "fills in" a number.
- **Guideline facts and license determinations are human-verified.** Claude may draft; an
  oncology clinician (clinical accuracy) and the license reviewer (terms) sign off. Treat any
  guideline/helpline fact Claude produces as unverified until checked and dated.
- **Self-report / model-based / break / denominator caveats are mandatory and non-removable** — a
  draft missing them fails the lint before it reaches a reviewer.
- **No causal language** on associational trend data; **no ranking / name-and-shame.**

(Per CLAUDE.md, this is the donated lane: the CLI prepares the workspace and the human runs their own
agent; no funded-lane/runner/API-key execution is implied by the above.)

---

## 6. Ten concrete optimizations

1. **Add a `comparability` matrix as a first-class, CI-enforced artifact** — encode which series may
   be trended/compared (same basis, age band, look-back, jurisdiction); the build fails on an unsafe
   pairing. Closes finding 1a/1d/1f.
2. **First-class `guidelineRegime` + `breakYear` dimension** with pre-loaded breaks (USPSTF CRC 2021,
   breast 2024; BRFSS 2011 weighting; NHIS 2019 redesign); charts render discontinuities, never draw
   across a break silently. Closes 1b/1g.
3. **`biasDirection` field + directional self-report caveat** ("likely undercounts gaps; true coverage
   plausibly lower") rendered into every self-report series and explainer. Closes 1a.
4. **Pick and enforce a concrete readability target** (e.g., grade 8) and a colorblind-safe,
   uncertainty-visible chart spec as CI lints. Closes 1h.
5. **Separate-panel rule for survey vs. programme/administrative international data** — never one axis;
   loud method labels. Closes 1d.
6. **Minimum-detectable-change / overlapping-CI rule** before any series is described as a "change" or
   "trend" — documented and linted. Closes 1f.
7. **Guideline-plurality presentation rule** in the misread-checklist: where bodies disagree, show the
   disagreement, attributed and dated; never resolve to an action. Closes 1e.
8. **PLACES vs. BRFSS separation guard** — forbid mixing model-based credible intervals with survey
   sampling CIs in one comparison; label model-based estimates distinctly.
9. **Pre-register the comprehension check items** (the "what this rate does/doesn't say" test) and run
   them through an advocate panel before M3 ship — already implied; make the item bank a committed
   artifact so the ≥80% target is auditable.
10. **Embeddable widget + machine-readable metadata (schema.org/Dataset, Croissant) from day one of
    M4** so beneficiaries embed live, attributed charts rather than static screenshots — raises
    adoption odds, which is the binding success metric.

---

## 7. Parallel & perpendicular spin-offs

- **`incidence-explorers` (parallel):** the same canonical-model + provenance + caveat engine applied
  to incidence/mortality trends (SEER/USCS aggregate, OECD). Screening coverage is the leading edge;
  incidence/mortality is the lagging outcome — share infrastructure, keep "trends not causes."
- **`disparities-analysis` (parallel):** the suppression-safe, non-partisan subgroup-breakdown
  methodology generalizes to any public-health equity series; this project's small-cell guard and
  neutral-framing rules are the reusable core.
- **`prevention-info-open` (perpendicular):** the oncologist/advocate-reviewed, "not-medical-advice,"
  guideline-attribution explainer pattern is a reusable template for prevention education broadly
  (HPV vaccination, tobacco, etc.) — shared review pipeline and misread-checklist.
- **Embeddable widgets:** accessible, CC-BY, provenance-linked chart components newsrooms/OER channels
  drop into pages — a distribution layer serving all three siblings.
- **MCP server:** expose the harmonized dataset + comparability matrix as a read-only MCP tool so other
  agents can query "breast screening coverage trend for state X, with caveats" and receive
  provenance + caveats + safe-comparison guardrails inline — turns the dataset into agent-accessible
  infrastructure (read-only, no advice, caveats mandatory in every response).

---

## 8. Open questions

1. **Comparability authority:** who owns and signs off the comparability matrix — is "safe to compare"
   a statistical-reviewer call, and what's the escalation when a beneficiary wants a comparison the
   matrix forbids?
2. **International scope vs. method-mixing risk:** is any survey-vs-programme international comparison
   worth shipping in v1, given finding 1d, or defer all cross-country to a panel-segregated later
   release?
3. **Lung screening data maturity:** lung (high-risk only, recent guideline, sparse/young BRFSS data)
   may be too thin for a defensible trend — include with heavy caveats, or defer past v1?
4. **CanScreen5 as cite-only peer vs. excluded:** IARC's licensing is unspecified in public sources;
   confirm terms before even cite-only reuse (the closest "harmonized repository" competitor —
   worth a direct license query to IARC).
5. **NC/SA policy resolution** (WHO GHO / IARC): cite-only forever, or a vetted NC-compatible
   derivative track? (Plan default: cite-only/exclude until decided.)
6. **CC-BY vs. CC0** for US-PD-only series, and how to license a *mixed-provenance* harmonized table.
7. **Beneficiary-first vs. dataset-first sequencing** given the 2027-03-31 build-vs-mothball rule —
   does securing a newsroom/OER adopter early reshape which indicator/geography leads M1?
8. **MCP / widget surface:** does exposing an agent-queryable dataset create a new "advice-creep"
   surface (an agent paraphrasing a trend into a recommendation) that needs its own guardrail spec?

---

### Source list

- CDC PLACES / US Cancer Statistics tools: https://www.cdc.gov/united-states-cancer-statistics/about/tools.html ; https://www.cdc.gov/united-states-cancer-statistics/dataviz/index.html
- CDC patterns/trends in screening: https://www.cdc.gov/pcd/issues/2018/17_0465.htm
- NCI/CDC State Cancer Profiles: https://statecancerprofiles.cancer.gov/risk/ ; https://sparkmap.org/data-dive-state-cancer-profiles/
- Up-to-date US screening 2021 (Cancer Trends context): https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10625435/
- OECD Health at a Glance 2025 — cancer screening: https://www.oecd.org/en/publications/2025/11/health-at-a-glance-2025_a894f72e/full-report/cancer-screening_a3f047dd.html
- Eurostat cancer screening statistics: https://ec.europa.eu/eurostat/statistics-explained/index.php?title=Cancer_screening_statistics
- WHO/IARC CanScreen5: https://canscreen5.iarc.fr/ ; https://www.nature.com/articles/s41591-023-02315-6
- Our World in Data (cancer): https://ourworldindata.org/grapher/cancer-death-rates-by-type-who-db
- Cancer Research UK screening news: https://news.cancerresearchuk.org/2024/12/19/90000-cancer-cases-caught-by-screening-over-last-five-years/
- County Health Rankings mammography measure: https://www.countyhealthrankings.org/health-data/community-conditions/health-infrastructure/clinical-care/mammography-screening
- Self-report accuracy (breast/cervical): https://www.ncbi.nlm.nih.gov/books/NBK539386/ ; https://www.researchgate.net/publication/340203094
- Over-reporting masks disparities: https://pmc.ncbi.nlm.nih.gov/articles/PMC10890357/
- USPSTF CRC 45 uptake: https://jamanetwork.com/journals/jamanetworkopen/fullarticle/2824357
- USPSTF breast age 40 (2024): https://www.breastcancer.org/news/new-screening-guidelines-USPSTF
