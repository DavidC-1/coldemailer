---
name: thebrain
description: Query "thebrain" — a self-storage investment knowledge base (RAG over Pinecone) for grounded answers about self-storage, markets, deal structures, underwriting, DSCR, seller financing, and operator best practices. Use when writing cold-email copy, ICPs, or lead magnets that need accurate self-storage domain facts instead of guessing. Returns a synthesized answer plus cited sources.
---

# thebrain

thebrain is a retrieval-augmented knowledge base over a curated library of self-storage
investment books, reports, and operator notes. When campaign copy, ICP definitions, or
lead-magnet content needs a real domain fact — not a guess — ask thebrain instead of
inventing numbers or claims.

## When to use

- Writing or reviewing self-storage outreach copy and you need an accurate fact (e.g. typical
  occupancy, NOI levers, what owners actually care about).
- Building an ICP or list filter that depends on how the self-storage market is structured.
- Drafting a lead magnet and you want claims grounded in source material with citations.
- Any "what's true about self-storage / markets / deal structures" question.

Do **not** use it for live prospect data, email-sending mechanics, or deliverability — those
are other skills.

## Setup

Add your key to `.env` (it is gitignored):

```
BRAIN_API_KEY=your-brain-api-key-here
BRAIN_URL=https://eutotlzasihspitgmzer.supabase.co/functions/v1/brain
```

`BRAIN_URL` is optional — the endpoint below is the default.

## How to call it

```bash
curl -s -X POST "https://eutotlzasihspitgmzer.supabase.co/functions/v1/brain" \
  -H "Authorization: Bearer $BRAIN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"question": "What DSCR should I require on a seller-financed deal?"}'
```

Optional body fields:

- `top_k` (int, default 6) — how many knowledge-base passages to retrieve.
- `filters` (object) — exact-match metadata filters, e.g. `{"type": "book"}`.

## Response shape

```json
{
  "answer": "A synthesized, cited answer grounded in the knowledge base.",
  "sources": [
    { "text": "...", "source": "...", "chapter": "...", "mentor_name": "...", "file": "...", "score": 0.83, "type": "book" }
  ]
}
```

Use `answer` for the grounded claim and cite from `sources` when you need attribution. If the
sources don't contain the answer, thebrain says so — don't paper over it with invented facts.
