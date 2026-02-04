# PRD: AI Chat Assistant for Employee Enrollment

**Owner:** Dan Duncan | **Status:** Draft for Engineering Grooming | **Date:** February 2026

---

## Problem Statement

Employees enrolling in benefits through BenefitBay face a high-stakes, once-a-year decision with unfamiliar terminology and complex tradeoffs. Analysis of ~7,300 support tickets shows:

| Issue | Tickets | Avg Touches | Complexity |
|-------|---------|-------------|------------|
| Plan selection / recommendations | 410 | 10.8 | Critical |
| Coverage questions | 250 | 7.2 | High |
| Premium / cost confusion | 173 | 9.9 | Critical |
| Plan change requests | 171 | 10.2 | Critical |
| Prescription coverage | 169 | 4.1 | High |
| Provider/doctor search | 162 | 8.0 | High |
| HSA/HRA/FSA + ICHRA questions | 271 | 6.1 | High |

**Core insight:** Users aren't asking generic questions—they're asking questions specific to *their* situation, *their* medications, *their* doctors, at *their* stage in the enrollment flow.

---

## Outcomes (How We Measure Success)

| Metric | What It Tells Us |
|--------|------------------|
| **Conversion impact** | Do users who engage with AI complete enrollment at higher rates? |
| **Escalation rate** | When do users abandon AI for human help? (Lower = better) |
| **Chat engagement by page** | Where do users need the most help? |
| **Satisfaction (thumbs up/down)** | Is the AI actually helpful? |
| **Questions logged** | What gaps exist in our UI that we should fix? |

---

## Guiding Principles

1. **Empathy first** — The goal is to guide employees to answers quickly, not deflect from support. Escalation to humans must always be available.

2. **Context-aware** — The AI knows where the user is in the flow, their family info, location, and which plans they're considering. Responses should feel personalized ("For a family of three in Kansas City...").

3. **Spanish language support** — Required from day one for all user-facing interactions.

4. **No medical advice** — The assistant helps with benefits decisions, not healthcare decisions.

5. **Grounded in data** — Responses should be based on actual plan data, formularies, and knowledge base content—not hallucinated.

---

## Scope: MVP for Employee Enrollment

### In Scope
- Chat widget embedded in the enrollment flow (sidebar or inline)
- Context-aware responses based on: current page, user profile, selected/considered plans
- Knowledge base integration (42+ articles, weighted search)
- Suggested questions that change by enrollment step
- Formulary lookup (medication coverage by plan)
- Human escalation path
- Question logging for product insights
- Spanish language support

### Out of Scope (Future)
- Provider network search integration
- Interactive cost calculator
- Document upload / verification
- Claims status checking
- Voice interface
- Cross-session memory / persistent history

---

## User Scenarios

**Scenario 1: "Which plan covers my medication?"**
User is on the plan comparison page and asks about Lipitor. The AI checks formulary data and responds: "Lipitor is Tier 2 on Plan A ($35 copay) and Tier 3 on Plan B ($50 copay). The generic, Atorvastatin, is Tier 1 on both plans at $10."

**Scenario 2: "What's the difference between HMO and PPO?"**
User is on the medical plan page. The AI provides a concise explanation from the knowledge base, tailored to the plans they're viewing.

**Scenario 3: "I picked the wrong plan, can I change it?"**
User is on the submit page. The AI checks if they're within the enrollment window and explains the change process, or offers to connect them with support.

**Scenario 4: "¿Cuál plan es mejor para una familia?"**
User asks in Spanish. The AI responds in Spanish with family-relevant plan guidance.

---

## Key Learnings from Prototype

These findings may inform engineering decisions:

- **Conditional context loading is essential** — Sending full plan data on every request is wasteful. Only load plans on plan selection pages, formularies when medications are mentioned.
- **Token budget matters** — Prototype ran 1,500–6,200 tokens per request depending on context. Production should have a defined ceiling.
- **Suggested questions reduce friction** — Page-specific questions (auto-cycling, click-to-send) drove engagement in testing.
- **PDF processing is the bottleneck** — Formulary PDF download takes 3-5 seconds; caching or pre-processing is critical.
- **Streaming improves perceived speed** — Claude API calls take 1-3 seconds; streaming would help UX.

---

## Open Questions for Grooming

1. **Memory scope** — Session-only (cleared on refresh) or persistent (tied to user account)?
2. **Escalation flow** — Live handoff, scheduled callback, or email ticket creation?
3. **Response streaming** — Required for MVP or can follow?
4. **Widget placement** — Always-visible sidebar vs. expandable chat bubble?
5. **Analytics infrastructure** — Where do logged questions go? PostHog events? Separate store?

---

## Key Flows

*These flows are starting points for design and engineering to refine together. They will become more detailed as we iterate.*

---

### Flow 1: Medication Coverage Question

**Context:** User is on the Medical Plan page, comparing 3 plans. They take daily medication and want to know which plan covers it best.

```
┌─────────────────────────────────────────────────────────────────────┐
│ MEDICAL PLAN SELECTION                              [Chat widget]   │
│                                                                     │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐            ┌─────────────┐ │
│  │ Plan A   │ │ Plan B   │ │ Plan C   │            │ 💬 Need     │ │
│  │ Gold PPO │ │ Silver   │ │ Bronze   │            │ help?       │ │
│  │ $450/mo  │ │ $320/mo  │ │ $275/mo  │            │             │ │
│  └──────────┘ └──────────┘ └──────────┘            │ User types: │ │
│                                                     │ "Does Plan  │ │
│                                                     │ A cover     │ │
│                                                     │ Metformin?" │ │
│                                                     └─────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

**Step 1: User asks about medication**
> "Does Plan A cover Metformin?"

**Step 2: AI detects medication keyword, loads formulary data**
- System recognizes "Metformin" as a drug name
- Fetches formulary for Plan A (cached if available)
- Searches for medication coverage tier

**Step 3: AI responds with specific coverage info**
> "Yes, Metformin is covered under Plan A. It's a Tier 1 (Generic) medication with a $10 copay for a 30-day supply.
>
> Would you like me to compare Metformin coverage across all three plans you're viewing?"

**Step 4: User asks for comparison**
> "Yes, compare all three"

**Step 5: AI provides comparison**
> "Here's how Metformin is covered across your options:
>
> | Plan | Tier | 30-day copay | Annual cost |
> |------|------|--------------|-------------|
> | Plan A (Gold PPO) | Tier 1 | $10 | $120 |
> | Plan B (Silver HMO) | Tier 1 | $10 | $120 |
> | Plan C (Bronze) | Tier 2 | $25 | $300 |
>
> Plans A and B have the same medication cost. The difference would come down to other factors like your deductible and doctor visits."

**Key moments for design:**
- Loading state while formulary is fetched (may take 1-3 seconds)
- Comparison table formatting in chat bubble
- Follow-up suggested question: "What other medications do you take?"

**Edge cases to consider:**
- Medication not found in formulary → "I couldn't find [drug] in Plan A's formulary. This could mean it's not covered or listed under a different name. Would you like me to connect you with support to verify?"
- User asks about brand vs. generic → Show both options with cost difference
- Formulary PDF unavailable → Provide link to carrier's drug search tool

---

### Flow 2: Plan Recommendation (Discovery → Suggestion)

**Context:** User is on the Medical Plan page, hasn't selected anything yet, and asks for help choosing.

**Step 1: User asks open-ended question**
> "Which plan should I pick?"

**Step 2: AI asks discovery questions (one at a time)**
> "I can help you find the right plan. Let me ask a few questions to understand your needs.
>
> First: How often do you typically visit a doctor in a year?
> - Rarely (0-1 visits)
> - Sometimes (2-4 visits)
> - Regularly (5+ visits)"

**Step 3: User responds**
> "Regularly, I have a chronic condition"

**Step 4: AI asks follow-up**
> "Got it. Do you take any ongoing prescription medications?"

**Step 5: User responds**
> "Yes, I take Humira for arthritis"

**Step 6: AI asks final question**
> "Last question: Is keeping your current doctor important, or are you open to switching?"

**Step 7: User responds**
> "I need to keep my rheumatologist"

**Step 8: AI provides personalized recommendation with reasoning**
> "Based on what you've shared—regular doctor visits, ongoing specialty medication (Humira), and needing to keep your specialist—here's my recommendation:
>
> **Plan A (Gold PPO)** is likely your best fit.
>
> Here's why:
> - **Lower out-of-pocket for frequent visits:** $25 copay vs. $50 on Bronze
> - **Humira coverage:** Tier 3 with $150 copay (vs. $200 on other plans)
> - **PPO network:** More flexibility to see specialists without referrals
>
> Your estimated annual cost with Plan A: ~$7,200
> (Premium: $5,400 + ~$1,800 in copays/medications)
>
> Would you like to see how this compares to Plan B, or do you have questions about Plan A?"

**Key moments for design:**
- Discovery questions could be quick-reply buttons (not just free text)
- Progress indicator: "Question 2 of 3"
- Recommendation should feel conclusive, not wishy-washy
- Show the math—users trust recommendations more when they see reasoning

**Edge cases to consider:**
- User skips questions → AI makes best guess with caveats
- User gives conflicting info → AI asks for clarification
- No clear "best" plan → AI presents top 2 with tradeoffs, doesn't force a choice

---

### Flow 3: Escalation to Human Support

**Context:** User has asked several questions, but the AI can't fully resolve their issue—or the user explicitly asks for human help.

**Trigger scenarios:**
- User says "I want to talk to someone" / "Can I speak to a human?"
- AI has responded 3+ times and user is still asking variations of same question
- Question involves account-specific data AI can't access
- User expresses frustration ("this isn't helping")

**Step 1: AI recognizes escalation trigger**
> User: "This is confusing, I just want to talk to someone who can help"

**Step 2: AI acknowledges and offers escalation**
> "I understand—benefits decisions can be complex, and sometimes it helps to talk it through with a person.
>
> I can connect you with our benefits support team. How would you like to reach them?
>
> - 📞 **Schedule a call** (next available: tomorrow 10am)
> - 💬 **Live chat with support** (available now)
> - 📧 **Send a message** (response within 24 hours)"

**Step 3: User selects option**
> [User clicks "Live chat with support"]

**Step 4: AI hands off with context**
> "Connecting you now. I'll share a summary of our conversation so you don't have to repeat yourself.
>
> **Summary for support team:**
> - Maria is enrolling for a family of 3 in Kansas City
> - She's comparing Plans A and B
> - Questions about Humira coverage and keeping her rheumatologist
> - Needs help understanding total annual cost"

**Step 5: Handoff complete**
> "You're now chatting with Sarah from the BenefitBay support team. I'll be here if you need me later!"

**Key moments for design:**
- Escalation option should always be visible (persistent "Talk to a human" link?)
- Context summary is crucial—don't make user repeat themselves
- Warm handoff > cold handoff (support sees conversation history)
- Clear visual transition from AI → Human chat

**Edge cases to consider:**
- Support unavailable (outside hours) → Offer callback scheduling or email
- User escalates immediately without trying AI → That's okay, don't block them
- Long conversation history → Summarize key points, don't dump full transcript

---

### Flow 4: Spanish Language Interaction

**Context:** User's browser is set to Spanish, or they type their first message in Spanish.

**Step 1: User asks question in Spanish**
> "¿Cuál es la diferencia entre el Plan A y el Plan B?"

**Step 2: AI detects Spanish and responds in Spanish**
> "¡Buena pregunta! Aquí está la comparación entre los dos planes:
>
> **Plan A (Gold PPO) - $450/mes**
> - Deducible: $1,500
> - Copago médico: $25
> - Red: PPO (más flexibilidad)
>
> **Plan B (Silver HMO) - $320/mes**
> - Deducible: $2,500
> - Copago médico: $35
> - Red: HMO (necesita referencia para especialistas)
>
> Para una familia de tres en Kansas City, el Plan A cuesta más cada mes pero tiene costos más bajos cuando visita al médico.
>
> ¿Tiene alguna pregunta específica sobre estos planes?"

**Step 3: Suggested questions appear in Spanish**
- "¿Cómo funciona la contribución de mi empleador?"
- "¿Qué plan cubre mis medicamentos?"
- "¿Puedo agregar dependientes más tarde?"

**Key moments for design:**
- Language detection should happen automatically (first message or browser setting)
- All UI elements in chat (buttons, suggested questions) should switch to Spanish
- Knowledge base articles should have Spanish versions
- Don't ask "Would you like to continue in Spanish?"—just do it

**Edge cases to consider:**
- User switches languages mid-conversation → AI follows their lead
- Mixed language input (Spanglish) → Respond in whichever language they used more
- Spanish content not available for specific topic → Respond in Spanish but note "I don't have detailed information about [X] in Spanish yet. Here's what I can share..." (don't fall back to English without warning)

---

### Flow 5: First-Time User Onboarding

**Context:** User lands on enrollment for the first time. Chat widget is visible but user hasn't engaged yet.

**Step 1: Chat widget shows welcome state**
```
┌─────────────────────────┐
│  💬 Need help choosing? │
│                         │
│  "Which plan is best    │
│   for my family?"       │
│                         │
│  "What does deductible  │
│   mean?"                │
│                         │
│  "¿Necesitas ayuda?"    │
│                         │
│  [Ask a question...]    │
└─────────────────────────┘
```

**Step 2: User clicks a suggested question**
> [Clicks "Which plan is best for my family?"]

**Step 3: AI responds with personalized context**
> "Hi Maria! I see you're enrolling coverage for yourself, Carlos, and Sofia in Kansas City.
>
> The 'best' plan depends on how you use healthcare. Can I ask a few quick questions to point you in the right direction?"

**Key moments for design:**
- Suggested questions visible before user engages (reduces blank-page anxiety)
- First response acknowledges user by name + family context (shows AI "knows" them)
- Mix of English and Spanish suggested questions signals bilingual support
- Don't overwhelm—3-4 suggested questions max

---

## Notes for Design + Engineering Sync

**For Jason (Design):**
- Chat widget placement: Sidebar (always visible) vs. floating bubble (expandable)?
- How do suggested questions cycle/display? Auto-rotate? Static list?
- Visual treatment for AI vs. human messages if we do handoff
- Loading/typing indicator design (streaming text vs. "thinking" dots)
- Mobile responsive behavior—where does chat live on small screens?

**For Engineering:**
- What's the latency budget? 2 seconds acceptable? 5 seconds too slow?
- Streaming: Server-sent events vs. polling vs. WebSocket?
- Context passing: How does chat know current page + user state?
- Session handling: What happens on page refresh? New session or resume?
- Error states: API timeout, formulary not found, Claude rate limit

---

## References

- [AI Chat Assistant (Notion hub)](https://www.notion.so/AI-Chat-Assistant-2e7c604c9865809984ecee1cd09d334c)
- [Prototype Learnings](/Product/chat-prototype-learnings.md)
- [Seed Questions (Notion)](https://www.notion.so/2e2c604c9865800eb3dfe9d569f18d7c)
- [Requirements Database (Notion)](https://www.notion.so/3c4afdf61d894042ae81dadc305a0071)
