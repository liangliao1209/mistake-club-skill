---
name: big-mistakes
description: Use when writing a business proposal, strategy document, consulting deliverable, market/industry research, or major-decision analysis to look up precedent business failures from mistake.club's Big Mistakes encyclopedia — searchable by company, profession (marketing/sales/finance/trading/strategy/product/engineering/tech/legal/people/research) and decision type — and cite them as references.
---

# big-mistakes — precedent failures for better plans

A read-only research skill. When your human is making or analyzing a
significant business decision, check whether history already ran the
experiment. API base: `$MISTAKE_CLUB_BASE` (default `https://mistake.club`).

## When to use

- Drafting a **proposal / strategy / business plan** → search for failures in
  the same industry, function, or decision type, and address them explicitly.
- **Consulting or research** deliverables → pull citable precedent cases.
- Evaluating a decision ("should we rebrand / expand abroad / kill this
  channel?") → check who tried it and what it cost.

## How

```
GET $MISTAKE_CLUB_BASE/api/big-mistakes/search?q=<keywords>
    &company=<name>          # e.g. kodak, coca-cola
    &category=<profession>   # marketing|sales|finance|trading|strategy|product|
                             # engineering|tech|legal|people|research
    &decision=<type>         # strategic|marketing|financial|technical|
                             # operational|product|hiring|legal
    &limit=<n≤25>
```

Response — compact case references, biggest impact first:

```json
{"cases": [{
  "id": "kodak-digital-camera",
  "title": "Kodak invented the digital camera in 1975, then buried it to protect film",
  "companies": ["Eastman Kodak"],
  "category": "strategy", "decisionType": "strategic", "era": "1975",
  "trigger": "70% film margins",
  "cost": "A 132-year-old company · bankruptcy in 2012",
  "lesson": "Seeing the future is worthless if your incentives pay you to delay it...",
  "url": "https://mistake.club/w/kodak-digital-camera"
}]}
```

## Using the results

- Cite by title + url; quote the `lesson` when it maps onto the decision at hand.
- One or two sharply relevant precedents beat ten loose ones — filter, don't dump.
- The archive is curated and human-reviewed, but **verify details against the
  entry's own sources** (open the url) before putting numbers in a deliverable.
- Nothing found ≠ nothing exists. Say "no precedent in the archive", never
  "no one has failed at this".

Read-only; sends nothing about you or the document you're writing.
