# AI Chat Assistant - Project Learnings for PRD

This document captures all learnings from the BenefitBay AI Chat Prototype that should inform the PRD for our AI chat assistant.

---

## 1. Architecture & Structure

### What Worked Well
- **Full-stack separation**: Python FastAPI backend + React frontend provides clean API boundaries
- **Context-aware design**: AI knows current page, user data, and selected options at all times
- **Conditional context loading**: Only load relevant data (plans, formularies, KB articles) when needed to optimize token usage

### Key Architecture Decisions
- **Stateless sessions**: Backend doesn't persist conversation history (acceptable for prototype, not for production)
- **JSON data sources**: Plans and knowledge base stored as JSON files for easy iteration
- **PDF caching**: Downloaded formulary PDFs cached to disk with MD5 hash filenames

---

## 2. Token Optimization Strategy

### Token Budget Breakdown
| Component | Tokens | When Loaded |
|-----------|--------|-------------|
| Base system prompt | ~800 | Always |
| Knowledge base results | ~500 | When query matches KB |
| Plan data | ~1,000 | Only on plan selection page |
| Formulary PDF content | ~200-400 | Only when medication keywords detected |
| **Typical total** | **1,500-6,200** | Per request |

### Learnings
- **Conditional loading is essential**: Don't send full plan data on every request
- **Smart keyword detection**: Extract medication names before deciding to load formulary data
- **Separate system prompt sections**: Makes it easy to include/exclude context blocks

---

## 3. Context Injection Patterns

### What Context the AI Receives
1. **Page context**: Which step in the flow the user is on
2. **User data**: Name, location, family size, DOB
3. **Selection state**: Currently selected plan (if any)
4. **Conversation history**: Previous messages in session
5. **Knowledge base**: Top 3 relevant articles based on query
6. **Formulary data**: Medication coverage info when asked about prescriptions

### Key Learning
Define your context taxonomy early. Every piece of context has a token cost—be intentional about what gets sent when.

---

## 4. Suggested Questions

### Implementation Pattern
```
verify_info page: 5 questions about ICHRA, address, family
medical_plan page: 9 questions about plan types, costs, HSA
submit page: 6 questions about coverage start, changes
```

### What Worked
- **Page-specific suggestions** reduce cognitive load
- **Auto-cycling** (8-second rotation) keeps UI dynamic
- **Hide on input focus**, show when input empty
- **Click to send** immediately submits the question

### Learnings for PRD
- Suggested questions should be contextual, not generic
- Consider user's progress through the flow
- Questions should anticipate user needs at each stage

---

## 5. Knowledge Base Design

### Structure That Worked
```json
{
  "id": "unique_id",
  "title": "Article Title",
  "category": "enrollment|plans|coverage|support|...",
  "tags": ["keyword1", "keyword2"],
  "content": "Full article text",
  "related_pages": ["verify_information", "medical_plan"]
}
```

### Search Algorithm
- **Weighted scoring**: tags (4pts), title (2pts), content (1pt), page bonus (1pt)
- **Returns top 3** most relevant articles
- **Injected into system prompt**, not returned directly to user

### Learnings
- Tags are more valuable than full-text search for relevance
- Page context should boost article scores
- 42 articles was sufficient for prototype; plan for 100+ in production

---

## 6. PDF & Document Processing

### What Was Implemented
- Download formulary PDFs on demand
- Cache to local disk to avoid re-downloading
- Extract text using pypdf library
- Regex search for medication names in extracted text

### Performance Observations
- PDF download: 3-5 seconds
- Text extraction: <1 second for cached PDFs
- Search within PDF: <50ms

### Learnings for PRD
- Plan for document caching from day one
- Consider pre-processing PDFs during ingestion, not at query time
- For production: vector embeddings for semantic search within documents

---

## 7. UI/UX Patterns

### Chat Widget Design
| Element | Implementation |
|---------|---------------|
| Position | Sidebar-embedded (450px height) |
| Message bubbles | User=right/blue, Assistant=left/gray |
| Input | Text field + send button, Enter to submit |
| Loading | Animated bouncing dots |
| Suggested questions | Below messages, auto-cycling |

### Layout Architecture
- **Left sidebar** (320px): Navigation + chat widget
- **Main content**: Enrollment pages
- **Right panel** (600px slide-in): Plan detail modal

### Key UX Learnings
- Persistent chat in sidebar keeps AI accessible without interrupting main flow
- Detail panels for rich information prevent chat from becoming overwhelming
- Visual feedback (loading states, focus rings) is essential

---

## 8. Conversation Memory

### Current Implementation
- **Frontend**: Maintains message array in component state
- **Backend**: Receives conversation history with each request but doesn't persist
- **Session scope**: Memory cleared on page refresh

### Production Gaps
- No cross-session memory
- No user authentication to tie conversations to accounts
- No conversation summarization for long chats

### Learnings for PRD
- Define memory scope: session only vs. persistent
- Plan for token limits with long conversations (summarization strategy)
- Consider conversation handoff to human agents

---

## 9. Response Style Guidelines

### What Was Defined in System Prompt
- **Factual questions**: Short, direct answers
- **Recommendations**: Include reasoning and cost breakdowns
- **Plan comparisons**: Use structured format with bullet points
- **Medication queries**: Show tier and coverage status clearly

### Discovery Question Pattern
The AI asks clarifying questions before making recommendations:
1. "How often do you visit the doctor?"
2. "Do you have any ongoing prescriptions?"
3. "Are you planning any procedures this year?"

Then provides personalized recommendation with cost math.

### Learnings
- Define response length expectations per question type
- Include example responses in system prompt
- Cost calculations should show the math, not just the result

---

## 10. Error Handling

### Backend Patterns
- Try-catch on all external operations (PDF download, API calls)
- Graceful fallback when formulary not found (provide URL instead)
- Logging throughout for debugging

### Frontend Patterns
- Loading states prevent interaction during API calls
- Error messages displayed in chat as assistant messages
- Form validation before submission

### Learnings for PRD
- Define error message templates for common failures
- Plan for graceful degradation (what does the AI say when KB is unavailable?)
- User-friendly error messages, not technical errors

---

## 11. API Contract Design

### Endpoints Implemented
| Endpoint | Purpose |
|----------|---------|
| `POST /api/chat` | Main chat endpoint |
| `GET /api/plans` | Return all plan data |
| `GET /api/knowledge-base` | Return full KB |
| `GET /api/knowledge-base/search` | Search KB with page context |
| `GET /api/placeholder-questions` | Suggested questions |

### Chat Request Shape
```json
{
  "message": "user question",
  "context": {
    "current_page": "medical_plan",
    "user_data": { "name": "...", "family_size": 3 },
    "selected_plan": "plan_b"
  },
  "conversation_history": [
    { "role": "user", "content": "..." },
    { "role": "assistant", "content": "..." }
  ]
}
```

### Learnings
- Context should be structured, not free-form
- Conversation history format should match LLM expectations
- Consider streaming responses for better UX (not implemented in prototype)

---

## 12. Performance Benchmarks

### Observed Performance
| Operation | Time |
|-----------|------|
| KB search | <50ms |
| PDF download (first time) | 3-5 seconds |
| PDF search (cached) | <100ms |
| Claude API call | 1-3 seconds |
| End-to-end response | 2-5 seconds |

### Learnings
- PDF operations are the bottleneck—pre-cache where possible
- Claude API latency is acceptable but streaming would improve perceived speed
- Knowledge base search is fast enough for real-time

---

## 13. Production Readiness Gaps

### Intentionally Excluded from Prototype
- [ ] User authentication/login
- [ ] Database persistence
- [ ] Conversation history storage
- [ ] Response streaming
- [ ] Rate limiting per user
- [ ] Vector embeddings for semantic search
- [ ] Conversation handoff to humans
- [ ] Analytics and logging infrastructure
- [ ] Multi-language support
- [ ] Accessibility audit

### Known Technical Debt
- Query punctuation not stripped in KB search
- Single-letter terms match too broadly
- No retry logic on API failures
- Hardcoded model name (should be configurable)

---

## 14. Key Recommendations for PRD

### Must-Haves
1. **Define context taxonomy**: What data does the AI need and when?
2. **Token budget**: Set limits and plan optimization strategies
3. **Memory scope**: Session vs. persistent, summarization strategy
4. **Error handling**: User-friendly messages for all failure modes
5. **Response streaming**: Essential for good UX with LLMs

### Should-Haves
1. **Suggested questions**: Context-aware, page-specific
2. **Knowledge base**: Weighted search with metadata
3. **Document processing**: PDF/doc ingestion pipeline
4. **Analytics**: Track question types, response quality, user satisfaction

### Nice-to-Haves
1. **Human handoff**: Escalation path when AI can't help
2. **Multi-modal**: Image upload for document analysis
3. **Proactive assistance**: AI suggests help based on user behavior
4. **Personalization**: Learn user preferences over time

---

## 15. System Prompt Structure

### Sections That Worked
1. **Role definition**: "You are a benefits enrollment assistant..."
2. **Context injection**: Current page, user data, selected plan
3. **Response guidelines**: Length, format, tone
4. **Domain knowledge**: Insurance fundamentals, cost formulas
5. **Knowledge base results**: Top 3 relevant articles
6. **Constraints**: What not to do, out-of-scope topics

### Example Pattern
```
You are [ROLE] helping users with [DOMAIN].

Current context:
- Page: {current_page}
- User: {user_name} from {location}, family of {family_size}
- Selected plan: {plan_name or "None yet"}

Response guidelines:
- For factual questions: Be concise (1-2 sentences)
- For recommendations: Show your reasoning and math
- For comparisons: Use bullet points or tables

Relevant knowledge:
{top_3_kb_articles}

You may NOT:
- Provide medical advice
- Make claims about coverage without checking formulary
- Skip the discovery questions before recommending a plan
```

---

## 16. Data Model Insights

### Plan Data Structure
Key fields that proved essential:
- Premium (individual + family rates)
- Deductible (in-network + out-of-network)
- Out-of-pocket maximum
- Copays by service type
- HSA eligibility
- Network type (HMO, PPO, HDHP)
- Formulary URL

### User Data Structure
Minimum viable user context:
- Name (for personalization)
- Location (affects plan availability)
- Family size (affects premium calculations)
- Selected plan (for contextual answers)

---

## 17. Integration Learnings

### Anthropic Claude Integration
- Model: claude-sonnet-4-20250514
- Max tokens: 1024 (sufficient for most responses)
- Temperature: Default (not specified)
- No function calling used (could enhance plan comparisons)

### Learnings
- Consider function calling for structured operations (plan lookup, cost calculation)
- Max tokens should vary by question type
- Monitor rate limits (10K tokens/min observed)

---

## Summary

This prototype validated several key patterns for AI chat assistants:

1. **Context-aware responses** dramatically improve relevance
2. **Token optimization** is essential for cost control
3. **Suggested questions** reduce user friction
4. **Knowledge base integration** keeps AI grounded in accurate information
5. **Conditional data loading** balances completeness with efficiency

The main gaps to address in production are:
- Persistent memory and user authentication
- Response streaming for better UX
- Vector embeddings for semantic search
- Human handoff escalation path
- Comprehensive analytics

Use these learnings to inform requirements, prioritization, and technical architecture decisions in the PRD.
