# Evidence Summary

**Project:** Cadence (Hospice Acuity & Staffing)
**Author:** Dr. Sharilyn Rennie
**Date:** 2026-05-18
**Scope:** Home hospice (routine, continuous home care) and inpatient hospice (general inpatient, IPU, respite). Palliative care consult and home health are out of scope.

## Composite acuity formula

Acuity score (0 to 100, plus event boost):

```
acuity = 0.20 * PPS_inverted
       + 0.30 * symptom_impact
       + 0.20 * phase_of_illness
       + 0.20 * level_of_care
       + 0.10 * psychosocial
       + event_boost
```

Tier mapping:

| Tier | Score | Roman | Descriptor |
|---|---|---|---|
| 1 | 0 to 24 | I | Stable |
| 2 | 25 to 44 | II | Watchful |
| 3 | 45 to 59 | III | Active |
| 4 | 60 to 74 | IV | Crisis-risk |
| 5 | 75+ | V | Dying / Maximum support |

## Instruments and benchmarks anchoring each component

### PPS (Palliative Performance Scale, weight 0.20)
- 11-point scale, 0% to 100% in 10% decrements, developed by Victoria Hospice Society.
- Strong correlation with survival (r ≈ 0.77 in PPSv2 validation).
- PPS at or below 50% is hospice-eligible for most non-cancer diagnoses; 70% for oncology.
- Originally validated for "analysis of home nursing care workload" (Anderson 1996), making it the natural functional anchor for a workload tool.
- Source: ePrognosis UCSF; Anderson F. et al., J Palliat Care 1996.

### HOPE Symptom Impact (weight 0.30)
- CMS-mandated hospice assessment effective 2025-10-01, replacing HIS.
- Eight symptoms (pain, dyspnea, anxiety, nausea, vomiting, diarrhea, constipation, agitation) rated 0 to 3 at admission and HOPE Update Visits (HUVs).
- Any single rating of 2 (moderate) or 3 (severe) triggers an in-person Symptom Follow-up Visit within 2 calendar days.
- Carries the highest weight because it directly mandates additional visits and therefore directly drives workload.
- Source: CMS HOPE Guidance Manual v1.01.

### Phase of Illness (weight 0.20)
- Five-state framework (Stable, Unstable, Deteriorating, Dying, Deceased).
- Validated inter-rater reliability across community, hospital, and hospice settings (Masso 2015).
- Stronger single predictor of palliative care intensity than diagnosis or age.
- Source: Masso M. et al., Palliat Med 2015; Mather 2018 cross-sectional analysis.

### Level of Care (weight 0.20)
- Five Medicare levels: RHC, CHC, Respite, IPU general, GIP.
- Each level encodes an embedded staffing intensity expectation:
  - RHC: ~1.8 hours skilled nursing per RHC day at industry average.
  - CHC: 8 hours direct care minimum, > 50% nursing per 42 CFR.
  - GIP: 24/7 RN, typically 1:1 nursing during a crisis.
- Source: 42 CFR § 418.302, CMS hospice manual.

### Psychosocial (weight 0.10)
- Reflects core hospice services beyond nursing (medical social work, spiritual counseling).
- Captures flags: sole caregiver, complex family dynamics, spiritual distress, food/housing insecurity, pediatric patient.
- Source: 42 CFR § 418.64 (Conditions of Participation, Core Services).

### Event boost (additive)
- Recent admission: +6 (first 7 days carry admit-visit and orientation workload).
- Imminent death (last 7 days): +15 (SIA window, daily visits expected).
- Recert due: +4.
- Source: CMS Hospice Visits When Death Is Imminent measure development.

## Visit-time defaults seeded into the tool

| Tier | Routine RN visit (min) | Visits / week | Rationale |
|---|---|---|---|
| I  | 45 | 1 | CMS family experience benchmark ("4 to 5 visits per week about an hour each" averages out across tiers). |
| II | 50 | 2 | Mid-range observed in time-motion studies. |
| III | 55 | 3 | Active deterioration, HOPE SFV triggers. |
| IV | 60 | 4 | Crisis-risk, often pre-SIA escalation. |
| V  | 75 | 6 | Dying, SIA-eligible, often daily visits per CMS hospice imminent-death measure. |

Other defaults:
- Admission visit: 90 min
- Death visit: 90 min
- Recert: routine + 30 min
- Travel: 1.4× multiplier when explicit drive minutes are not provided
- IDG / indirect time: folded into productivity factor (default 0.65)

## Staffing benchmarks

- **NHPCO Hospice Staffing Framework (March 2024):** No mandated caseload. Recommends individualized adjustment.
- **Home hospice RN caseload (Multi-View Benchmarking):** 12 to 14 per RN case manager (target). Industry reality 16 to 24. Default in tool: 13.
- **Inpatient hospice RN ratio:** Federal floor 1:8 plus at least 2 direct-care staff and at least 1 RN per shift when any GIP patient is present. IAHPC recommended optimum 1:1.5 to 1:1.7. Default in tool: 1:1.6.
- **CHC:** 8 hours direct care, > 50% nursing.
- **SIA:** 15-minute increments up to 4 hours/day, RN/MSW only, paid at CHC hourly rate. Cumulative +15.7 minutes per beneficiary over the last 7 days post-implementation.
- **CAPC consult productivity:** 2,100 to 2,400 wRVU per provider per year, 335 consults per FTE per year (national average 251). Used for reference only; this tool does not produce palliative consult forecasts.

## Adaptive learning

- The tool stores every logged visit (tier at visit, visit type, direct minutes, travel minutes).
- For each (tier, visit type) pair, when n ≥ 5 the tool replaces the seeded default with the rolling mean.
- The Forecast tab shows source ("Learned" vs "Default") and n for transparency.
- Future enhancement: confidence intervals, decay weighting so older visits matter less than recent ones, separate weights per staff member.

## What this tool does not do

- Does not produce HOPE assessment submissions (that is the EHR's job).
- Does not generate billing claims or SIA documentation.
- Does not compute palliative consult productivity or RVU targets.
- Does not store PHI of any kind. The private-ID model and HIPAA Safe Harbor de-identification approach are documented in `compliance-notes.md`.

## Sources

- [CMS HOPE](https://www.cms.gov/medicare/quality/hospice/hope) — CMS Hospice Outcomes and Patient Evaluation tool, effective 2025-10-01.
- [CMS HOPE Guidance Manual v1.01](https://www.cms.gov/files/document/hope-guidance-manualv101.pdf) — Item definitions and HUV schedule.
- [Palliative Performance Scale, ePrognosis](https://eprognosis.ucsf.edu/pps.php?p=hospice) — UCSF.
- [PPS validation (Polish hospice setting)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7178730/) — survival correlation r ≈ 0.77.
- [IPOS validation, Sterie et al. 2019](https://pubmed.ncbi.nlm.nih.gov/31185804/) — internal consistency α = 0.77.
- [Phase of Illness cross-sectional analysis](https://pmc.ncbi.nlm.nih.gov/articles/PMC5788082/) — Mather et al.
- [Phase of Illness inter-rater reliability, Masso 2015](https://pubmed.ncbi.nlm.nih.gov/25249239/).
- [AN-SNAP v5](https://www.ihacpa.gov.au/resources/australian-national-subacute-and-non-acute-patient-classification-version-50) — Australian palliative casemix classification.
- [ESAS-r Administration Manual, Alberta Health Services](https://www.albertahealthservices.ca/assets/info/peolc/if-peolc-ed-esasr-admin-manual.pdf).
- [NHPCO Hospice Staffing Framework, March 2024](https://allianceforcareathome.org/wp-content/uploads/NHPCO_Hospice_Staffing_Framework.pdf).
- [Multi-View Caseload Expectations](https://mvib.net/CaseloadExpectations.htm) — industry benchmark 12 to 14.
- [42 CFR § 418.64 Core Services](https://www.ecfr.gov/current/title-42/chapter-IV/subchapter-B/part-418/subpart-C/subject-group-ECFR35b48a647589673/section-418.64).
- [Hospice Continuous Home Care, PCNOW FF #507](https://www.mypcnow.org/fast-fact/ff-507-hospice-continuous-home-care/).
- [SIA evaluation, Cagle 2017](https://pubmed.ncbi.nlm.nih.gov/28622481/) — last-7-days visit patterns and SIA impact.
- [IAHPC Human Resources Guide](https://iahpc.org/resources/publications/getting-started/human-resources/) — IPU staffing recommendations.
- [CAPC Productivity Benchmarking](https://www.capc.org/blog/productivity-benchmarking-in-palliative-care-what-we-know-and-where-to-start/) — wRVU and consult-volume context.
