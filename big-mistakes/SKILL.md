---
name: big-mistakes
description: Use whenever the human is writing or evaluating a business plan — a proposal, strategy document, market entry, pricing change, acquisition, launch, reorganisation, consulting deliverable or investment memo. Cross-checks the plan against mistake.club's archive of documented business failures, finds the organisations that already tried the same thing, and writes the precedent into the plan so it addresses what went wrong for them.
---

# big-mistakes — cross-check a plan against what already failed

Most plans fail in a way somebody has failed before. This skill finds those
somebodies and puts them in the document, with sources.

API base: `$MISTAKE_CLUB_BASE` (default `https://mistake.club`). Read-only.
**Never send the human's document.** You extract search terms yourself; only
those terms leave the machine.

## When to run it

Proactively, without being asked, whenever the work involves a real decision:
market entry, pricing, rebrand, acquisition or merger, a big technical bet, an
outsourcing decision, a launch date, a reorganisation, a new channel, a
cost-cutting programme, a compliance shortcut.

Skip it for pure information requests ("what is EBITDA") and for creative work
with no decision in it.

## The five steps

### 1. Read the plan and name its exposures

Not topics — **exposures**: the specific decisions that could go wrong. Write
them down before searching. A plan usually has three to six.

> "Open 12 big-box stores in tier-1 China" → exposures: *format transplanted
> into a different consumer culture*, *heavy fixed cost before demand is
> proven*, *local competitors with lower cost structure*.

### 2. Turn each exposure into two or three query angles

Mix the vocabularies — the archive indexes companies, professions, decision
types and plain description:

| Angle type | Example |
|---|---|
| the mechanism, in plain words | `q=china market entry` |
| the format or instrument | `q=big-box retail` |
| a comparable company | `company=home depot` |
| the function that owns it | `category=strategy` |
| the kind of decision | `decision=strategic` |

### 3. Ask in one round trip

```
GET $MISTAKE_CLUB_BASE/api/big-mistakes/search
      ?q=<angle>&q=<angle>&q=<angle>      # repeat q for each angle
      &detail=full                        # adds why[] and sources[] — needed to argue
      &category=<profession>              # optional filters
      &decision=<type>
      &company=<name>
```

Professions: `marketing sales finance trading strategy product engineering tech
legal people research`. Decision types: `strategic marketing financial technical
operational product hiring legal`.

The reply tells you which angle found what, the deduplicated union, and where
cases repeat:

```json
{
  "angles": [{ "q": "china market entry", "found": ["home-depot-china", "..."] }],
  "cases": [{
    "id": "home-depot-china", "title": "…", "companies": ["The Home Depot"],
    "category": "sales", "decisionType": "strategic", "era": "2006–2012",
    "trigger": "a DIY format in a do-it-for-me market",
    "cost": "every big-box store closed by 2012",
    "lesson": "…", "why": ["…", "…"], "tags": ["retail", "china"],
    "sources": ["https://…"], "url": "https://mistake.club/w/home-depot-china"
  }],
  "repeats": { "byDecision": [{ "value": "strategic", "count": 3, "ids": ["…"] }] }
}
```

### 4. Select — three precedents beat ten

Keep a case only if the **mechanism** matches, not merely the industry. A
retail bankruptcy caused by a leveraged buyout is not a precedent for a retail
format that misread demand. Ask of each: *would this failure have happened to
this plan, for this reason?* If no, drop it.

Two to four cases in the deliverable. If two cases say the same thing, keep the
better documented one.

### 5. Write it into the plan, not into an appendix

For each surviving precedent, one block:

- **The exposure** — which decision in the plan this is about.
- **Who already did it** — company, year, one line on what they did.
- **What it cost** — the figure from `cost`, quoted, not rounded up.
- **Why it failed** — from `why[]`, in your own words, the mechanism.
- **What this plan does differently** — or, if it doesn't, say that plainly.
  This line is the whole point; without it the precedent is trivia.
- **Source** — the `url`, so the reader can check you.

Then, if `repeats` shows the same decision type or category more than once,
add one short paragraph naming the shared mechanism across those cases. That
paragraph is the analysis; the cases are the evidence for it.

Finish with what you did **not** find: the angles that returned nothing.
Silence in the archive is not safety — say so in those words.

The full output template and a worked example are in `reference.md`. Read it
the first time you run a cross-check.

## Rules

- **Never invent a case, a number or a URL.** Everything you assert comes from
  a returned field. If you want to say something the archive does not support,
  attribute it to yourself, not to the archive.
- **Quote `cost` and `era` verbatim.** They are the facts most likely to be
  misremembered.
- **Verify before it ships.** The archive is human-reviewed but not infallible;
  open the `url` for any figure that will appear in a client deliverable.
- **Nothing found ≠ nothing exists.** Say "no precedent in the archive for X",
  never "no one has failed at this".
- **Do not moralise.** These are decisions that looked rational at the time.
  Explain the mechanism; don't call anyone stupid.
- **Read-only, and quiet.** The archive learns nothing about the human, the
  company or the document.
