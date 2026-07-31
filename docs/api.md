# API reference

> **The engine matches words, not meaning.** Relevance is a keyword score over
> how each case is written up. Query with the distinctive nouns of the
> failure's world — `q=DIY home improvement china` puts Home Depot's China
> exit first; `q=china market entry` returns whatever big case shares a word.
> Several narrow angles beat one broad one, and the multi-`q` form exists for
> exactly that.


Base URL: `https://mistake.club`
Read-only. No account, no key, no rate limit worth worrying about.

Everything the skill does goes through one endpoint. It is public, so you can
call it by hand while you are figuring out what to ask.

---

## `GET /api/big-mistakes/search`

Find documented failures that match one or more angles.

### Parameters

| Parameter | Type | Notes |
|---|---|---|
| `q` | string, **repeatable** | Free text. Repeat it to ask several angles in one round trip — the response then reports which angle found what. |
| `company` | string | Substring match on the organisations involved: `kodak`, `coca-cola`, `home depot`. |
| `category` | enum | The profession that owns the decision. One of `marketing` `sales` `finance` `trading` `strategy` `product` `engineering` `tech` `legal` `people` `research`. |
| `decision` | enum | The kind of decision. One of `strategic` `marketing` `financial` `technical` `operational` `product` `hiring` `legal`. |
| `detail` | `full` | Adds `oneLiner`, `why[]`, `tags[]`, `significance` and `sources[]`. Costs tokens, so it returns fewer cases. |
| `limit` | integer | Max cases. Capped at 25 compact, 8 detailed. |

### How ranking works

Matches are graded by **where** the query landed, not how often it appears:

1. the organisation that made the mistake (`companies[0]`) — exact, then prefix
2. another organisation named in the case
3. the title
4. the tags
5. the body

`significance` (1–100) only breaks ties inside a grade. So `?q=apple` returns
Apple's own failures before cases where Apple is a bystander, and never
matches "Snapple".

Multi-word queries are scored token by token: a case that answers two words of
`china market entry` outranks one that answers a single word.

### Single-angle response

```json
{
  "cases": [
    {
      "id": "home-depot-china",
      "title": "Home Depot shut all its big-box stores in China — DIY met a 'do-it-for-me' culture",
      "companies": ["The Home Depot"],
      "category": "sales",
      "decisionType": "strategic",
      "era": "2006–2012",
      "trigger": "a DIY format in a do-it-for-me market",
      "cost": "all big-box stores closed by 2012",
      "lesson": "A format that works at home is a hypothesis abroad…",
      "url": "https://mistake.club/w/home-depot-china"
    }
  ]
}
```

### Multi-angle response

Ask three angles at once:

```
GET /api/big-mistakes/search?q=china+market+entry&q=big-box+retail&q=cultural+risk&detail=full
```

```json
{
  "angles": [
    { "q": "china market entry", "found": ["kfc-china-yum-spinoff", "..."] },
    { "q": "big-box retail",     "found": ["home-depot-china", "..."] }
  ],
  "cases": [
    {
      "...": "every field above, plus:",
      "oneLiner": "In 2006 Home Depot bought a Chinese chain…",
      "why": [
        "Home Depot exported a do-it-yourself format into a market that was fundamentally 'do-it-for-me'…",
        "It assumed a booming housing market meant demand for DIY retailing…"
      ],
      "tags": ["retail", "china", "market-entry"],
      "significance": 62,
      "sources": ["https://en.wikipedia.org/wiki/The_Home_Depot"]
    }
  ],
  "repeats": {
    "byCategory": [{ "value": "strategy", "count": 6, "ids": ["…"] }],
    "byDecision": [{ "value": "strategic", "count": 8, "ids": ["…"] }]
  }
}
```

- **`angles[]`** — which angle produced which cases. Use it to tell a risk
  with precedent from one without.
- **`cases[]`** — the deduplicated union, best matches first, each case once.
- **`repeats`** — where the returned cases share a category or decision type.
  This is a mechanical count, not an insight: it tells you *that* something
  repeats. Deciding what the repetition means is your job.

### Field meanings

| Field | What it is |
|---|---|
| `trigger` | The small decision that started it — ≤40 characters, deliberately. |
| `cost` | What it actually cost, in the archive's own words. **Quote it; don't round it up.** |
| `lesson` | One transferable sentence. Usable when it maps onto the decision at hand. |
| `why[]` | The root causes. This is the material for explaining *why* the precedent applies. |
| `significance` | 1–100, business impact. 90+ is rare; most real cases sit 50–70. |
| `era` | The year or span. Quote it verbatim. |
| `sources[]` | Where the facts came from. Open them before a number goes into a deliverable. |

### Errors

There is no error envelope. An unknown `category` or `decision` simply matches
nothing, and you get `{"cases": []}`. Treat an empty result as "no precedent in
the archive", never as "no such failure exists".

---

## Trying it by hand

```bash
# the biggest documented failures, no query
curl "https://mistake.club/api/big-mistakes/search?limit=5"

# one company
curl "https://mistake.club/api/big-mistakes/search?company=kodak"

# a profession, deepest cases first
curl "https://mistake.club/api/big-mistakes/search?category=finance&limit=10"

# a real cross-check: three angles, root causes included
curl "https://mistake.club/api/big-mistakes/search\
?q=subscription+pricing&q=customer+churn&detail=full"
```

## What the server never sees

The agent extracts the angles from your document locally and sends only those
words. The document itself, the client's name, the numbers in your model — none
of it is transmitted. There is no account, no cookie and no logging tied to who
is asking.
