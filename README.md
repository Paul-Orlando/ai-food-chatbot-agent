# Food Chatbot Agent (Flowise)

### Agentic RAG — v1

A production-ready food ordering chatbot built in [Flowise](https://flowiseai.com). Designed for restaurant use cases — handling menu queries, ingredient lookups, pricing, and end-to-end order processing through a multi-tool sequential agent architecture.

---

## Key Capabilities

- Natural language menu and ingredient lookups via a RAG-powered reasoning tool
- Real-time price arithmetic via a dedicated calculator tool
- Guided order collection and confirmation via a structured chain tool
- Persistent conversation memory via Postgres (Neon)
- Input moderation on both the agent entry point and the order chain
- Deterministic, template-driven order confirmations via `returnDirect`

---

## Architecture

```
User → [OpenAI Moderation] → Start → Agent → End
                                        |
                    ┌───────────────────┼────────────────────┐
                    ▼                   ▼                     ▼
           chatbot_reasoning        calculator            take_order
           (Chatflow Tool)       (Calculator Tool)     (Chain Tool)
                                                             |
                                                       [LLM Chain]
                                                             |
                                                    [OpenAI Moderation]
                                                             |
                                                     [Prompt Template]
```

**Flow:** `Start → Agent → End`

The agent uses GPT-4o as its backbone and routes queries to one of three tools based on explicit system prompt rules.

---

## Tool Routing Logic

| User Intent | Tool Used |
|---|---|
| Menu questions, ingredients, allergens, pricing | `chatbot_reasoning` |
| Price totals, subtotals, arithmetic | `calculator` |
| Placing a confirmed order | `take_order` |
| General questions (hours, greetings) | Direct response — no tool |

`take_order` has a hard prerequisite: items, quantities, and prices must all be confirmed before the tool fires.

---

## Tech Stack

- [Flowise](https://flowiseai.com) — Agent orchestration
- GPT-4o (OpenAI) — LLM backbone
- OpenAI Moderation API — Input safety on two layers
- Neon (Postgres) — Persistent agent memory
- LangChain Sequential Agents — Graph execution engine

---

## Configuration Highlights

| Parameter | Value | Reason |
|---|---|---|
| Temperature | 0.2 | Deterministic responses for a transactional bot |
| Max Tokens | 600 | Keeps responses concise, controls cost |
| Max Iterations | 10 | Prevents runaway agent loops |
| returnDirect | true | Order confirmations bypass agent rewording |
| strictToolCalling | false | Required for Calculator tool schema compatibility |
| Memory | Postgres (Neon) | Persistent across sessions |

---

## Setup Instructions

### Prerequisites
- Flowise v1+ installed and running
- OpenAI API key
- A separate RAG chatflow deployed in Flowise (for `chatbot_reasoning`)
- Neon (or any Postgres) database for agent memory

### Import

1. Download `Agentic_RAG_Agents_improved.json`
2. In Flowise, go to **Chatflows → Import**
3. Select the JSON file
4. Configure the following credentials in Flowise:
   - **OpenAI** — your OpenAI API key (used by both the agent LLM and moderation nodes)
   - **Postgres** — your Neon connection details (host, database, port, username, password)
5. Update the **Chatflow Tool** node to point to your RAG chatflow ID
6. Save and test

### Postgres SSL (Neon)

Add this to the **Additional Config** field on the Postgres Agent Memory node:

```json
{
  "ssl": {
    "rejectUnauthorized": false
  }
}
```

---

## Notes

- The `chatbot_reasoning` Chatflow Tool references a specific chatflow ID (`96b18633-...`). Replace this with your own RAG chatflow ID after importing.
- The moderation error message is set to: *"Sorry, I can only help with food orders and menu questions. Please try a different message!"* — update to match your brand.
- For local/development testing, the Postgres memory node can be removed entirely. The agent will still maintain context within a single session.

---

## Version History

See [changelog.md](./changelog.md) for full version history.

---

## Author

Paul Orlando
Creative Technologist | AI Agent Developer | Data Analytics
🌐 [paulforlando.com](https://www.paulforlando.com)
