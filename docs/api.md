# API reference

> **The engine matches words, not meaning.** Query with the distinctive nouns
> of the failure's world — `q=DIY home improvement china` puts Home Depot's
> China exit first; `q=china market entry` returns whatever big case shares a
> word. Several narrow angles beat one broad one — one call per angle.

Base URL: `https://mistake.club`

## Authentication

Every endpoint requires a member token, sent as a header — no `Bearer` prefix:

```
authorization: mc_skill_<token>
```

Tokens are free: register at https://mistake.club, open the skill page, and
download your personalized skill copy (handle and token embedded) or copy the
token from there. A call without a valid token comes back **`401`** — that is
the answer, not a retryable error.

Your token also decides what you can see. Every member is on one of two tiers:

- **core** — the 5,000-case core archive. Free, forever.
- **full** — the entire archive, 6,000+ cases and growing. Opens at 50
  approved community contributions.

Search results and case fetches are scoped to your tier.

---

## `GET /api/skill/status`

The member's standing, in one call:

```json
{
  "tier": "core",
  "coreTotal": 5000,
  "fullTotal": 6200,
  "approved": 31,
  "pending": 4,
  "unlockAt": 50,
  "remaining": 19,
  "message": "You're on the core tier — 5,000 cases. 19 more approved contributions unlock the full archive."
}
```

- **`tier`** — `core` or `full`.
- **`approved` / `pending`** — the member's reviewed and in-queue
  contributions.
- **`unlockAt` / `remaining`** — the full-archive threshold and how far away
  it is.
- **`message`** — a ready-to-relay English sentence about the member's
  access. Skills built on this API end every run by passing it on to the
  user, in the agent's own words.

## `GET /api/archive/search`

Find documented failures matching one angle. Repeat the call per angle.

| Parameter | Type | Notes |
|---|---|---|
| `q` | string | Free text. Use the distinctive nouns of the failure's world, not strategy-deck vocabulary. |
| `limit` | integer | Max cases returned. |

The response is a list of case summaries, scoped to the caller's tier:

```json
{
  "cases": [
    {
      "id": "home-depot-china",
      "title": "Home Depot shut all its big-box stores in China — DIY met a 'do-it-for-me' culture",
      "oneLiner": "In 2006 Home Depot bought a Chinese chain…",
      "era": "2006–2012",
      "category": "sales",
      "cost": "all big-box stores closed by 2012",
      "trigger": "a DIY format in a do-it-for-me market",
      "significance": 62
    }
  ]
}
```

Summaries are for selecting, not for citing. An empty list means "no
precedent in the archive you can see" — a core-tier search covers the core
archive — never "no such failure exists".

## `GET /api/archive/case/<id>`

The full case, in the archive's entry schema:

```json
{
  "id": "home-depot-china",
  "title": "…",
  "oneLiner": "…",
  "summary": ["…", "…"],
  "why": [
    "Home Depot exported a do-it-yourself format into a market that was fundamentally 'do-it-for-me'…",
    "It assumed a booming housing market meant demand for DIY retailing…"
  ],
  "cost": "all big-box stores closed by 2012",
  "trigger": "a DIY format in a do-it-for-me market",
  "lesson": "A format that works at home is a hypothesis abroad…",
  "sources": [{ "title": "…", "url": "https://…" }],
  "era": "2006–2012",
  "category": "sales",
  "significance": 62
}
```

A case outside the caller's tier answers **`403 {"error":"full_archive_only"}`**.
Leave it out of the deliverable; never argue from its summary alone.

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

---

## Contributing

Members on the way to 50 approved cases use two more endpoints. Contributing
is a dedicated session's work, never mixed into a cross-check.

### `GET /api/contribute/slots`

The assignment slots: the market × industry gaps the archive most needs
filled. Work a slot rather than freelancing duplicates of famous cases.

### `POST /api/contribute/submit`

```json
{ "entries": [ { "id": "…", "title": "…", "summary": ["…"], "why": ["…"], "cost": "…", "trigger": "…", "lesson": "…", "sources": [{ "title": "…", "url": "https://…" }] } ] }
```

Entries follow the same schema the case endpoint returns. Submissions land in
a human-reviewed queue, never straight into the archive. Approved cases go
live credited `community:@<your handle>`; rejected ones come back with a note
saying why and what to fix. `/api/skill/status` tracks the count.

---

## Trying it by hand

```bash
# your standing
curl -H "authorization: mc_skill_<token>" \
  "https://mistake.club/api/skill/status"

# one angle
curl -H "authorization: mc_skill_<token>" \
  "https://mistake.club/api/archive/search?q=subscription+pricing&limit=8"

# the full case behind a summary
curl -H "authorization: mc_skill_<token>" \
  "https://mistake.club/api/archive/case/home-depot-china"
```

## What the server never sees

The agent extracts the search terms from your document locally and sends only
those words, under your token. The document itself, the client's name, the
numbers in your model — none of it is transmitted.
