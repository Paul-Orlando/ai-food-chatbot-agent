# Changelog — Food Chatbot Agent

---

## v1 — 2026-05-22

### Initial Release

**Agent configuration:**
- GPT-4o backbone at temperature 0.2 for deterministic transactional responses
- Max tokens capped at 600
- Max iterations capped at 10 to prevent runaway loops
- Strict tool calling disabled for Calculator schema compatibility

**Tool setup:**
- `chatbot_reasoning` — Chatflow Tool for RAG-powered menu, ingredient, allergen, and pricing lookups
- `calculator` — for price arithmetic and order totals
- `take_order` — Chain Tool with `returnDirect: true` for deterministic order confirmations

**Prompts:**
- System prompt with explicit, rule-based tool routing
- Hard prerequisite gate on `take_order` (items + prices required before firing)
- Structured order confirmation prompt template using `{input}` variable, eliminating circular dependency

**Safety & reliability:**
- OpenAI Moderation connected on Start node (agent entry)
- OpenAI Moderation connected on LLM Chain node (order processing)
- Friendly, brand-neutral moderation error message

**Memory:**
- Postgres Agent Memory via Neon
- SSL config added for Neon connection requirement
