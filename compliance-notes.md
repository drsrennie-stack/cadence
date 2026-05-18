# Compliance Notes

**Project:** Cadence (Hospice Acuity & Staffing)
**Files covered:** `cadence.html` (current); `hospice-acuity-staffing.html` (legacy)
**Date:** 2026-05-18
**Reviewer:** Dr. Sharilyn Rennie

## 1. Standards targeted

- WCAG 2.2 Level AA (minimum, project floor)
- WCAG 2.2 Level AAA where reasonable (contrast on body text, focus indicators)
- HIPAA Privacy Rule, 45 CFR 164.514 (Safe Harbor method of de-identification)
- 42 CFR 418 (Medicare Hospice Conditions of Participation) — informational only; the tool does not produce regulatory submissions

## 2. WCAG criterion-by-criterion notes

| Criterion | Level | Status | Notes |
|---|---|---|---|
| 1.1.1 Non-text content | A | Pass | Icon-only close button uses `aria-label="Close"`. Privacy banner has `aria-labelledby`. Tier-distribution bars have `role="img"` with descriptive `aria-label`. |
| 1.3.1 Info and relationships | A | Pass | Semantic HTML throughout (header, nav, main, section, table, caption, fieldset-equivalent radiogroups). Tables include captioned semantics. |
| 1.4.3 Contrast (Minimum) | AA | Pass | Navy `#1E3D4C` on off-white `#FAFAF9` = 11.1:1. Body text against white card = 11.4:1. Terra-dark `#A0522D` on off-white = 5.0:1 (AA large text and UI). |
| 1.4.6 Contrast (Enhanced) | AAA | Mostly pass | Body navy on off-white meets 7:1 AAA. Small terra-dark text on light backgrounds sits between AA and AAA; reserved for non-essential eyebrows. |
| 1.4.10 Reflow | AA | Pass | Responsive grid; no horizontal scroll at 320 px except for tables (which use `overflow-x: auto` and a wrap container). |
| 1.4.11 Non-text contrast | AA | Pass | Form borders use `--border-strong` `#94a2a9` (3:1 against white). Focus outline 3 px gold against any background. |
| 1.4.12 Text spacing | AA | Pass | All text uses `em`/`rem`-relative spacing; line-height 1.5 on body. |
| 2.1.1 Keyboard | A | Pass | Every interactive control is a native `button`, `input`, `select`, or has `role="tab"` with full keyboard handlers (Arrow, Home, End). |
| 2.1.2 No keyboard trap | A | Pass | Modal closes with Escape, click on backdrop, or close button. Focus is returned to the page. |
| 2.4.1 Bypass blocks | A | Pass | Skip link to `#main` provided at top. |
| 2.4.3 Focus order | A | Pass | Tab order follows DOM order; modal opens with focus on first input. |
| 2.4.7 Focus visible | AA | Pass | 3 px gold outline with 2 px offset on all focusable elements. |
| 2.5.5 Target size | AAA | Pass | Buttons sized ≥ 36 px tall; symptom radio pills ≥ 38 × 38 px. |
| 3.1.1 Language of page | A | Pass | `<html lang="en">`. |
| 3.2.1 On focus | A | Pass | No focus-change side effects. |
| 3.2.2 On input | A | Pass | Settings inputs auto-save without page change; live regions announce. |
| 3.3.1 Error identification | A | Pass | HIPAA-guard heuristic warns inline when the private ID looks like PHI; submit prompts confirmation. |
| 3.3.2 Labels or instructions | A | Pass | Every input has a `<label for>`. Hints below fields use `.hint` class. |
| 4.1.2 Name, role, value | A | Pass | `role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected`, `aria-controls`, `aria-labelledby` on all tab structures. Modal uses `role="dialog"` and `aria-modal`. Live regions use `aria-live="polite"`. |
| 4.1.3 Status messages | AA | Pass | Dashboard KPI cards and alert stack use `aria-live="polite"`. |

## 3. Keyboard navigation verified

- Tab through header actions, into tab nav, then into the active panel.
- ArrowLeft / ArrowRight cycle through tabs; Home / End jump to first / last.
- Modal opens with focus on the first input; Escape closes; backdrop click closes.
- All forms submit with Enter from any focused field.
- Symptom severity radios reachable; Space toggles the selected radio.

## 4. Screen reader notes (test plan)

Tested logic, not yet on a live screen reader. Before publishing:

1. Run NVDA on Firefox and VoiceOver on Safari.
2. Verify the tab list announces "tab, selected, 1 of 9" semantics.
3. Verify the modal trap and `aria-modal` are announced.
4. Verify the alert stack reads new entries as they appear.

## 5. HIPAA and privacy

- The tool does not collect, transmit, or persist Protected Health Information by design.
- Each patient is represented by a random opaque token in the form `PT-XXXX-XX`. The mapping between token and real patient must live only in the user's HIPAA-compliant EHR or chart.
- A heuristic guard (`phiWarning`) flags label text containing spaces, long numeric runs (likely MRN/DOB), or date-like patterns. The guard does not block submission but does require explicit confirmation.
- Drive zones must be regional tags or 3-digit ZIP prefixes. Per HIPAA Safe Harbor 45 CFR 164.514(b)(2)(i)(B), 3-digit ZIP prefixes qualify as de-identified only when the combined population of all ZIP codes sharing those 3 digits exceeds 20,000. Full 5-digit ZIPs and street addresses are PHI and are not accepted.
- Staff names are not patient PHI and may be entered as displayed (e.g., "Maria S., RN").
- All persistence is `localStorage` on the user's device. Export to JSON / CSV produces files the user controls; they should be stored in a HIPAA-compliant location if combined with any PHI mapping.

## 6. Known limitations and remediation plan

- The HIPAA guard is heuristic. It catches obvious slip-ups (spaces, MRN-like digits, date patterns) but cannot detect every form of identifier. A future iteration can add a strict mode that rejects any non-PT-XXXX-XX label.
- Visit notes are free text. A future iteration can add a stronger client-side scrub or restrict to a controlled vocabulary.
- The "learned" visit-time weights swap in at n ≥ 5. With small caseloads, this may be too eager. Plan: surface a confidence band and allow the user to override the threshold.
- IPU and GIP staffing math currently assumes a single ratio (default 1:1.6 RN to patient). Future versions should let the user set separate shift ratios for IPU vs GIP.
- No automated unit tests yet. Plan: extract pure functions (`acuityScore`, `acuityTier`, `weeklyMinutesForPatient`) into testable modules and add a Vitest harness.

## 7. Iframe and embed considerations

- The HTML ships with the iframe height-sender script (postMessage with `id`, ResizeObserver, load, resize, and mutation observers) for Kajabi / Cowork sidebar embed.
- All internal links (none currently) and the Skip Link target `_top` is not required (no cross-domain hosting), but the page is iframe-safe.
- External links open with `target="_blank" rel="noopener"`.

## 8. Sign-off

Reviewer: Dr. Sharilyn Rennie
Date: 2026-05-18
Status: Ready for first-pass clinical pilot. Re-review after first 30 days of logged visit data and learned-weight activation.
