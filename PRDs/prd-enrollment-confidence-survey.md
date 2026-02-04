# PRD: Enrollment Confidence Survey

**Owner:** Dan Duncan (PM)
**Status:** Draft
**Created:** January 27, 2026

---

## Problem

BenefitBay is investing in redesigning the enrollment experience, but we have no quantitative measure of how confident employees feel about the choices they make during enrollment. Without a baseline metric, we cannot evaluate whether changes to the enrollment experience are actually improving outcomes for employees.

Employee confidence matters because healthcare enrollment is high-stakes, annual, and stressful. An employee who completes enrollment but feels uncertain about their selections is more likely to contact support, less likely to view BenefitBay positively, and may second-guess their coverage when they need it most.

## Objective

Establish a reliable confidence metric for QLE/special enrollment that serves as a baseline for measuring the impact of experience changes throughout 2026.

## Key Results

1. Ship a one-question micro-survey in the QLE/special enrollment flow with a **50% response rate** among eligible employees.
2. Establish a **baseline confidence score** over at least two weeks, segmented by device, enrollment type, and whether the employee used help.

## Scope

**In scope:**
- QLE / special enrollment flows

**Out of scope (for now):**
- Open Enrollment flows (designed to be extensible so OE can be added later)

---

## Solution

### Survey Design

**Placement:** Post-submission confirmation screen, displayed after the employee completes their QLE enrollment.

**Question:**

> How confident do you feel about the benefits you just selected?

**Scale:** 1–5 Likert

| Value | Label |
|-------|-------|
| 1 | Not at all confident |
| 2 | Slightly confident |
| 3 | Somewhat confident |
| 4 | Confident |
| 5 | Very confident |

**Language support:** The question and scale labels must be available in both English and Spanish.

### Interaction Details

- The survey appears on the confirmation/success screen after enrollment submission.
- It should be visually prominent but non-blocking — the employee can dismiss or ignore it without friction.
- No additional questions, follow-ups, or open text fields. One tap and done.
- After the employee selects a response, show a brief acknowledgment (e.g., "Thanks for your feedback") and collapse the survey.
- If the employee navigates away without responding, do not resurface the survey.
- The survey should appear only once per enrollment submission. If an employee submits multiple QLE enrollments, they see the survey each time.

### Display Rules

| Condition | Show survey? |
|-----------|-------------|
| Employee completes QLE enrollment | Yes |
| Employee completes OE enrollment | No (future phase) |
| Employee abandons enrollment | No |
| Employee has already responded for this enrollment | No |

---

## Data & Tracking

### Events to Capture

| Event | Properties |
|-------|-----------|
| `confidence_survey_shown` | `enrollment_id`, `enrollment_type` (QLE subtype), `device_type`, `language` |
| `confidence_survey_responded` | `enrollment_id`, `enrollment_type`, `device_type`, `language`, `score` (1–5), `used_help` (boolean — true if the employee interacted with support chat, AI assistant, or help content during the session) |
| `confidence_survey_dismissed` | `enrollment_id`, `enrollment_type`, `device_type` |

### Segmentation Dimensions

These dimensions are required for baseline analysis:

- **Device:** Desktop vs. mobile
- **Enrollment type:** QLE subtype (e.g., marriage, new hire, loss of coverage)
- **Used help:** Whether the employee used the AI chat assistant, contacted support, or accessed help content during their enrollment session
- **Language:** English vs. Spanish

### Reporting

Reporting destination to be determined during implementation. The data model should support flexible consumption — whether that ends up in PostHog dashboards, Domo, or both.

**Baseline dashboard should include:**
- Overall average confidence score
- Score distribution (histogram)
- Score by device type
- Score by enrollment type
- Score by help usage
- Response rate (responses / survey impressions)
- Volume over time (daily/weekly)

---

## Success Criteria

| Metric | Target |
|--------|--------|
| Survey response rate | >= 50% of employees who see the survey |
| Baseline data collection period | >= 2 weeks of data |
| Segmented data available | Scores broken out by device, enrollment type, and help usage |

## Risks & Considerations

- **Response rate risk:** 50% is an ambitious target for a post-submission survey. Placement and visual prominence on the confirmation screen are critical. If response rates are low, we may need to experiment with timing or presentation — but we should avoid making the survey blocking or intrusive.
- **Sample size:** QLE enrollment volume is lower than OE. Two weeks may not yield statistically significant segment-level data depending on volume. We should monitor volume and extend the collection period if needed.
- **"Used help" definition:** The `used_help` flag requires a clear definition of what counts. Recommendation: any interaction with the AI assistant, help content links, or support contact during the enrollment session.
- **Survey fatigue:** Keep this to exactly one question. Resist the temptation to add more. The value is in the trend line, not in a single detailed response.

## Future Considerations

- Extend to Open Enrollment flows when the next OE window opens.
- Correlate confidence scores with downstream metrics (support ticket volume, plan change requests, NPS).
- Consider adding an optional open-text follow-up for low scores (1–2) in a future iteration, if qualitative signal is needed.
- Use confidence score as an evaluation metric for A/B tests on enrollment experience changes.

---

## Open Questions

1. What QLE subtypes have enough volume to produce meaningful segment-level data within two weeks?
2. How is "help usage" currently tracked in the enrollment session — is there an existing event or session property we can reference, or does this need to be built?
3. Should the survey render as part of the existing confirmation page component, or as an overlay/modal?

---

## Appendix: Design & Research Reviews

### UI Design Review

#### Overall Assessment

This PRD is solid on intent and data requirements but underspecified on visual and interaction design. A designer picking this up would need to make a significant number of judgment calls that should be resolved at the PRD level, especially given the ambitious 50% response rate target. That target will live or die on the specifics of presentation.

#### Placement and Visibility

- **Position on the page** is unspecified. If the survey sits below a scrollable confirmation summary, many users will never see it — especially on mobile. This is the single biggest risk to the 50% response rate target.
- **Recommendation:** Resolve Open Question 3 as "inline card component, not overlay." Specify placement as immediately below the success/confirmation heading, above enrollment summary details. An overlay/modal contradicts "non-blocking," and bottom-of-page placement will tank response rates.
- Consider a bordered card with a slightly differentiated background color and clear visual separation from surrounding confirmation content — similar to how Stripe or Shopify surface post-action feedback prompts.

#### Scale Interaction Pattern

- **Visual rendering** is not specified. Are these radio buttons? Clickable numbered buttons? Stars? The rendering choice has major implications for tap target size, accessibility, and response rate.
- **Recommendation:** Use five horizontally arranged, equally sized pill-shaped buttons, each displaying the number and its label. Avoid stars and emoji — they carry connotations that don't map to "confidence." Selection triggers immediate submission with no confirm step.
- **Label visibility on mobile:** Consider always displaying endpoint labels ("Not at all confident" / "Very confident") with intermediate labels shown only on the selected button.

#### Acknowledgment Behavior

- **Do not collapse.** Replace the survey content in-place with a static acknowledgment message ("Thanks for your feedback" with a subtle checkmark). Collapsing causes layout shift — the surrounding content jumps upward, which is disorienting on a confirmation page the user may still be reading.
- If the team insists on collapsing, add a 2-second delay and use a smooth height animation (300ms ease-out).

#### Dismissal Behavior

- The PRD includes a `confidence_survey_dismissed` event but does not specify an explicit dismiss control (e.g., "Skip" link or X button). Without one, the event may never fire.
- **Recommendation:** Add a "Skip" text link below the scale buttons. Fire `confidence_survey_dismissed` only on explicit skip. Infer a third state ("ignored") from `shown` events with neither `responded` nor `dismissed`.

#### Accessibility (Significant Gap)

The PRD has no accessibility requirements. This needs a dedicated section:

- **Color contrast:** Scale must not rely on color alone to convey meaning (fails colorblind users, may not meet WCAG 2.1 AA).
- **Screen reader support:** Proper ARIA markup — each scale option should be a labeled radio input within a `<fieldset>` with a `<legend>`. Use `aria-live="polite"` for the acknowledgment.
- **Keyboard navigation:** Users must be able to tab to the survey and use arrow keys to select a value.
- **Touch targets:** Minimum 44x44px per WCAG 2.5.8. Five buttons on a 320px screen is workable (~64px each) only if labels are handled carefully.
- **Spanish label lengths:** Spanish labels are generally 20-30% longer than English. Layout must accommodate the longest label in both languages without breaking.

#### Mobile Responsiveness

- **Breakpoints needed:** On screens below 480px, switch to a vertical stack of full-width buttons. Above 480px, horizontal layout works.
- Ensure the card has at least 16px margin on all sides with clear separation from adjacent content.
- Thumb reachability is acceptable if the survey is near the top of the page, but it must be visually prominent enough that users notice it before scrolling.

#### Reference Pattern

The closest analogue is the **inline card on confirmation page** pattern (e.g., Shopify post-checkout feedback). Reference this explicitly so the designer has a clear mental model.

#### Summary of UI Recommendations

1. Resolve Open Question 3 as "inline card component, not overlay"
2. Specify placement hierarchy: success message > survey card > enrollment details
3. Define scale control: five horizontal pill buttons, immediate submit on tap
4. Define acknowledgment: replace in-place, do not collapse
5. Add a "Skip" text link and reconcile dismiss tracking
6. Add an Accessibility Requirements section
7. Add responsive breakpoints (horizontal above 480px, vertical stack below)
8. Reference the target interaction pattern (Shopify post-checkout inline feedback)

---

### UX Research Review

#### Overall Assessment

The single-question micro-survey approach is the right instinct for this context. However, there are several methodological concerns that, if left unaddressed, will undermine the team's ability to interpret the data reliably.

#### 1. Question Wording: Confidence vs. Comprehension

The question as worded conflates two sources of confidence: confidence in the *choices themselves* (did I pick the right plan?) and confidence in *understanding* those choices (do I understand what I selected?). An employee could feel confident they picked the best option but still not understand their deductible, or vice versa.

**Recommendation:** Decide which dimension matters more for the redesign goals. If the redesign is primarily about information clarity and decision support, consider: "How confident are you that you understand the benefits you just selected?" If it is about overall decision quality, the current wording is closer to correct. Document the intended interpretation so results are analyzed consistently.

#### 2. Scale Design: Ceiling Effect Risk

- The labels are all variations of "confident" (unipolar scale), which tends to compress responses toward the positive end. Combined with post-completion context, expect strong clustering at 4-5.
- The midpoint ("Somewhat confident") is ambiguous — could mean "I'm fine" or "I have real doubts."
- **Recommendation:** Commit to reporting **top-2-box percentage** (scores 4+5) as the primary metric rather than the mean. Document this before data collection begins.

#### 3. Timing Bias: Post-Submission "Relief Effect" (Most Significant Concern)

Post-submission placement introduces a well-documented completion bias. The employee has just finished a stressful task and the confirmation screen signals success — confidence is inflated by the relief of being done, not a reflective assessment of choices.

Research on post-task surveys shows responses collected immediately after completion skew 0.3-0.5 points higher (on a 5-point scale) vs. responses collected 24+ hours later. This means:

- The baseline will be artificially high
- The ceiling effect will be more pronounced
- Detecting improvement from experience changes becomes harder
- Real improvements may show no movement because the starting point was already inflated

**Recommendation:** Keep post-submission for response rate, but plan a validation study: send a follow-up email to a subset of respondents 48-72 hours later with the same question. Compare the distributions to quantify the relief effect in your specific population. This doesn't need to be in v1 but should be on the roadmap.

#### 4. Response Rate: 50% Is Likely Unrealistic

Benchmarks for optional, non-incentivized, post-task single-question surveys:

- In-app micro-surveys: 10-30% typical, 30-40% for well-designed implementations
- Post-transaction surveys: 15-25% typical
- NPS-style prompts: 20-35% in-app

QLE enrollments happen during stressful life events (loss of coverage, divorce, birth). Employees in these moments may be even less inclined to engage with optional feedback.

**Recommendation:** Set target at 25-30%. Treat anything above 30% as strong. More importantly, analyze non-response bias: are non-respondents systematically different (disproportionately mobile, certain QLE types)? Track `survey_shown` vs. `responded` by segment.

#### 5. Baseline Duration: Two Weeks May Be Insufficient

To establish a reliable baseline per segment, you need ~100-150 responses for a stable overall mean and sufficient volume in each segmentation cell.

If QLE volume is 50/week at 30% response rate = ~30 responses over two weeks. Not enough for an overall baseline, let alone segments. If 500/week = ~300 responses — adequate overall but thin for low-volume QLE subtypes.

**Recommendation:**
- Pull current QLE volume by subtype before committing to a timeline
- Set minimum sample size of n >= 30 per reportable segment
- Plan for 4-6 weeks with a check-in at two weeks
- Group low-volume QLE subtypes into broader categories (e.g., "life change" for marriage/birth/adoption)

#### 6. Missing Segmentation Dimensions

- **Employer/group:** Different employers have different plan counts and contribution structures. An employee choosing between 2 plans vs. 12 plans is a different experience.
- **First-time vs. returning:** First-time BenefitBay users will have a different confidence profile than repeat users.
- **Number of dependents:** Single vs. family enrollment is a different task in complexity.
- **Time spent in enrollment:** A behavioral proxy capturable passively. Correlations with confidence are informative.

**Recommendation:** Add employer (or plan count as a proxy) and first-time vs. returning. These likely already exist in session data.

#### 7. "Used Help" Flag Needs More Granularity

A single boolean collapses very different behaviors: opening a tooltip (light) vs. contacting live support (heavy). Without distinguishing type, you can't interpret the segment.

**Recommendation:** Capture subcategories:
- `viewed_help_content` (tooltips, FAQ, plan comparison tools)
- `used_ai_assistant` (AI chat interactions)
- `contacted_support` (live agent or support ticket)

Include a simplified `used_any_help` boolean for the survey event, but retain the richer data for analysis.

#### 8. Threats to Validity

| Threat | Mitigation |
|--------|------------|
| Relief / completion bias | Delayed follow-up validation study; interpret baseline as upper bound |
| Selection bias (non-response) | Analyze non-response by segment; report per-segment response rates |
| Social desirability bias | Minimal risk (anonymous, single question), but worth noting |
| Ceiling effect | Top-box analysis; consider 7-point scale |
| Seasonal / event-type confounding | Extend baseline; control for QLE type |
| Hawthorne effect | Allow 3-5 day burn-in before including data in baseline |
| Platform changes during baseline | Freeze enrollment UX changes during baseline window |

#### 9. Interpreting the Baseline

Guidance the PRD should include:

- **The absolute number is not meaningful in isolation.** There is no universal benchmark. A score of 4.1 is a starting point, not a verdict.
- **The value is in the delta.** Report as a range (mean +/- confidence interval), not a point estimate.
- **Watch the distribution, not just the mean.** A bimodal distribution (lots of 5s and lots of 1-2s) tells a different story than a normal curve, even at the same mean. Make the histogram the primary dashboard view.
- **Segment differences are hypotheses, not conclusions.** A 0.4-point gap between mobile and desktop is a signal to investigate, not proof that mobile is worse.
- **Set a minimum detectable effect (MDE) in advance.** Decide what improvement would be meaningful enough to act on and do a power analysis to confirm your sample sizes can detect it.

#### 10. Additional Recommendations

- **Pilot first:** Run with a single employer group for 3-5 days to validate response rate, check rendering, confirm event tracking, and verify Spanish translation.
- **Version the survey:** Include `survey_version` on all events from day one so question/scale/placement changes are trackable.
- **Plan the "so what" conversation:** Specify who reviews the baseline data, when, and what decisions it informs. A metric without a decision framework is just a number.
