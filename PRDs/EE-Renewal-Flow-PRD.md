# EE Renewal Flow PRD

> **Status**: Requirements Gathering
> **Focus**: EE (Dan)
> **Est. Start**: March 2026
> **Est. End**: May 2026

---

# What is this?

Updating the employee renewal flow to make benefit selection a simple 4-5 click process for employees with existing coverage. This introduces two streamlined paths: a **4-click "no change" flow** for employees whose plan hasn't changed year-over-year, and a **5-click "plan changed" flow** that clearly highlights what's different when their current plan has been modified.

---

# Problem Alignment

**The average employee gets through renewal in 67 clicks.** For what should be a straightforward annual task—confirming or updating existing coverage—this is far too involved. Analysis of 7,300+ OE 2025 support tickets revealed that one of the top user frustrations is: *"I just want the same coverage as what I already have."*

Additionally, user research shows that when plans *do* change year-over-year, employees struggle to understand what's different. One participant noted: *"It isn't super clear what has changed since last year… showing if benefits have decreased would be helpful."*

**Why this matters:**
- **Employee retention** is a higher priority than new employee enrollment (per leadership)
- 40% of employees select the same plan YoY—they shouldn't have to navigate a full shopping flow
- Renewal is lower-hanging fruit than new enrollment redesign
- Reduces support ticket volume related to renewal confusion

**Evidence:**
- OE 2025 ticket analysis: "I just want the same coverage" is a top-5 frustration
- Maze V2.1 testing: Participants wanted clearer indication of YoY changes
- Prototype testing: Plan comparison view during renewal was well-received
- Current flow: 67 clicks average to complete renewal

### Goals + Outcomes

| Metric | What It Tells Us | Goal |
|--------|------------------|------|
| **Clicks to complete renewal** | How streamlined is the happy path? | Reduce from 67 to ≤5 clicks for no-change renewals |
| **Renewal completion rate** | Are employees finishing the process? | Maintain or improve current baseline |
| **Time to complete renewal** | Is the process faster? | Reduce median time by 50%+ |
| **Support tickets (renewal-related)** | Are employees less confused? | Reduce renewal-related tickets by 25% |
| **Qualitative: Confidence** | Do employees feel good about their selection? | User research scores ≥4/5 on confidence |

---

# Solution Alignment

### In Scope

1. **4-Click "No Change" Renewal Flow** (plan unchanged YoY)
   - Step 1: Welcome/landing screen showing current plan summary
   - Step 2: Verify personal information (pre-populated, confirm or edit)
   - Step 3: Confirm plan selection with clear "Keep this plan" CTA
   - Step 4: Review + submit enrollment

2. **5-Click "Plan Changed" Renewal Flow** (plan modified YoY)
   - Step 1: Welcome screen with alert that plan has changed
   - Step 2: "Here's What Changed" comparison view highlighting:
     - Employer contribution changes
     - Premium/cost changes
     - Deductible changes
     - Max out-of-pocket changes
     - Provider network changes (if data available)
     - Prescription cost changes (if data available)
   - Step 3: Decision point—"Keep updated plan" or "Shop other plans"
   - Step 4: Verify personal information
   - Step 5: Review + submit enrollment

3. **YoY Comparison Display**
   - Clear visual indicators for increases/decreases (red/green or similar)
   - Side-by-side comparison on shopping page when user opts to explore other plans
   - Current plan highlighted/badged throughout shopping experience

4. **"Shop Other Plans" Escape Hatch**
   - Both flows allow users to exit into full plan shopping
   - Current plan should be clearly marked when shopping
   - Easy path back to "just keep my plan" if user changes mind

5. **Information Verification Step**
   - Pre-populated with existing data
   - Address validation (critical for plan availability)
   - Dependent verification (ages, coverage eligibility)

### Out of Scope

1. **Ancillary products** (dental, vision, life)—handled in separate PRD
2. **Full new enrollment redesign**—this focuses on renewal path only
3. **AI Chat Assistant integration**—covered in separate PRD (though renewal should support chat when available)
4. **Mobile-specific optimizations**—will follow desktop implementation
5. **QLE/Special Enrollment flows**—different trigger, different requirements

### Guiding Principles

1. **Respect the user's time**: 40% just want the same plan—let them confirm in under 60 seconds
2. **Transparency on changes**: When something *has* changed, make it unmissable
3. **No dead ends**: Always provide a clear path forward or back
4. **HIPAA compliance**: All data handling must meet existing compliance requirements
5. **Spanish language support**: All new UI must support localization
6. **Progressive disclosure**: Don't overwhelm—show details on demand

---

# Key Flows

### Flow 1: No-Change Renewal (4 clicks)

**Trigger**: Employee's current plan exists in next year's offerings with no material changes

1. **Welcome Screen**
   - Greeting with employee name
   - Summary card showing current plan: name, premium, employer contribution
   - Primary CTA: "Keep My Plan"
   - Secondary: "Shop Other Plans"

2. **Verify Information**
   - Pre-filled address, dependents
   - Confirm or edit
   - Clear indication why accuracy matters (plan availability)

3. **Confirm Selection**
   - Full plan summary
   - Cost breakdown (employer contribution vs. employee cost)
   - Coverage effective date
   - HIPAA/ICHRA acknowledgments (if not previously completed)

4. **Confirmation**
   - Success state with confetti/celebration
   - Clear next steps
   - When to expect ID cards
   - How to make changes if needed

### Flow 2: Plan Changed Renewal (5 clicks)

**Trigger**: Employee's current plan has material changes (cost, benefits, network, etc.)

1. **Welcome Screen with Alert**
   - Clear notification: "Your plan has changed for [year]"
   - Brief summary of current plan
   - CTA: "See What's Changed"

2. **What's Changed Comparison**
   - Side-by-side: Last Year vs. This Year
   - Highlighted changes with directional indicators:
     - ↑ Premium increased by $X/month
     - ↓ Deductible decreased by $X
     - ⚠️ [Provider] no longer in network
   - Primary CTA: "Keep Updated Plan"
   - Secondary: "Shop Other Plans"

3. **Decision Point** (if user proceeds with updated plan)
   - Confirmation of understanding changes
   - Or: Enter shopping flow with current plan badged

4. **Verify Information**
   - Same as Flow 1

5. **Confirm + Submit**
   - Same as Flow 1

---

# Open Questions

| Question | Status | Owner | Notes |
|----------|--------|-------|-------|
| What YoY comparison data is available today? (contribution, cost, deductible, providers, Rx) | 🔴 Needs validation | Engineering | Critical for "What's Changed" screen |
| How do we define "material change" that triggers Flow 2 vs Flow 1? | 🔴 Open | Product | Any cost change? Only >X%? |
| Can we access prior year plan details for comparison? | 🔴 Needs validation | Engineering | Required for comparison view |
| What's the fallback if comparison data is unavailable? | 🔴 Open | Product | Default to Flow 2 with generic "review your plan"? |
| How do we handle employees whose plan is discontinued entirely? | 🔴 Open | Product | Forced into shopping flow? |
| Should we show "You could save $X" messaging when cheaper alternatives exist? | 🟡 Discuss | Product/Design | Could drive engagement but adds complexity |

---

# Design

- [EE Renewal Flow Designs](https://www.notion.so/2fdc604c9865806783e8e9ae19a3c53d) — Jason Fallas
- Includes mockups for:
  - 4-click no-change flow
  - 5-click plan-changed flow with YoY comparison
  - Shopping page with change highlights

---

# Resources

- [EE Enrollment Redesign](https://www.notion.so/2eec604c986580e1b1d2dea0b70294fb) — Parent project
- [OE 25 Key User Frustrations](https://www.notion.so/2f5c604c9865809eb9a3eaf9d614ab41) — Support ticket analysis
- [Maze V2.1 Testing Results](https://www.notion.so/2e9c604c986580a4bda4ff760a71b4f4) — Prototype testing
- [Outstanding Questions](https://www.notion.so/2eec604c986580da8465e024517ba674) — Open items from redesign
