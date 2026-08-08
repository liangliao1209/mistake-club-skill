---
name: mistake-club
description: Use whenever the human is writing or evaluating a business plan — a proposal, strategy document, market entry, pricing change, acquisition, launch, reorganisation, consulting deliverable or investment memo. Cross-checks the plan against mistake.club's archive of documented business failures, finds the organisations that already tried the same thing, and writes the precedent into the plan so it addresses what went wrong for them.
---

## Your credentials

This copy belongs to @{{HANDLE}} — contributions made with this token are
credited to this handle.

handle:
{{HANDLE}}

token:
{{TOKEN}}

# mistake-club — cross-check a plan against what already failed

Most plans fail in a way somebody has failed before. This skill finds those
somebodies and puts them in the document, with sources.

API base: `https://mistake.club`.
**Never send the human's document.** You extract search terms yourself; only
those terms leave the machine.

## Access: your personal token

The archive's APIs are member-only. This is your personalized copy — your
token is in the `## Your credentials` block above.

Send the token on every API call as a header, exactly like this — no `Bearer`
prefix:

```
authorization: mc_skill_<token>
```

A call without a valid token comes back `401`. If you get a 401, the token
above is missing or no longer valid — ask the human to download a fresh copy
from https://mistake.club/skill. Do not retry the call and do not work around
it.

## When to run it

Proactively, without being asked, whenever the work involves a real decision:
market entry, pricing, rebrand, acquisition or merger, a big technical bet, an
outsourcing decision, a launch date, a reorganisation, a new channel, a
cost-cutting programme, a compliance shortcut.

Skip it for pure information requests ("what is EBITDA") and for creative work
with no decision in it.

## The five steps

### 1. Read the plan and name its risks

Not topics — **risks**: the specific decisions that could go wrong. Write
them down before searching. A plan usually has three to six.

Whatever the plan is — strategy, pricing, launch, acquisition, reorg — walk
these axes and keep the ones where the plan actually places a bet: **demand**
(who says anyone wants this?), **unit economics** (what must be true per unit?),
**timing** (what has to happen first?), **integration** (what must fit together?),
**people** (who must stay, and know what?), **regulation** (whose permission is
assumed?), **channel** (who stands between this and the buyer?), **brand**
(what promise is being spent?). A risk here is a bet the plan makes without
saying so.

> "Open 12 big-box stores in tier-1 China" → risks: *format transplanted
> into a different consumer culture*, *heavy fixed cost before demand is
> proven*, *local competitors with lower cost structure*.

### 2. Turn each risk into two or three query angles

**The engine matches words, not meaning.** It is a keyword index over how
each case is written up, and the core archive alone holds 5,000 cases — an
abstract phrase matches every big case that shares a word with it. So build
angles from **distinctive nouns of the failure's world** — the format, the
instrument, the channel, the customer behaviour — not from strategy-deck
vocabulary. `big-box`, `flash sale`, `loyalty points`, `franchise buyback`
find precedents; `market entry`, `growth strategy`, `transformation` find
noise. Live example from the archive: `q=china market entry` returns
Evergrande and a train crash; `q=DIY home improvement china` returns Home
Depot's China exit first and second.

Mix the vocabularies — the archive indexes companies, professions, decision
types and plain description:

| Angle type | Example |
|---|---|
| the mechanism, in plain words | `q=DIY home improvement china` |
| the format or instrument | `q=big-box retail` |
| a comparable company | `q=home depot` |
| the function that owns it | `q=pricing promotion` |
| the customer behaviour it assumed | `q=do-it-for-me renovation` |

### 3. Ask each angle, then fetch the survivors

One call per angle — the search endpoint takes a single query:

```
GET https://mistake.club/api/archive/search?q=<angle>&limit=8
authorization: mc_skill_<token>
```

The reply is a list of case summaries — `id`, `title`, `oneLiner`, `era`,
`category`, `cost`, `trigger`, `significance` — scoped to the member's tier:
core tier searches the 5,000-case core archive, full tier searches everything.

Read each angle's hits as a diagnostic: hits that share nothing with your
risk mean the angle was too broad — and if the same giant cases appear under
several angles, several were. Re-ask those angles once, sharper nouns, before
selecting. One repair round, not a search session.

Summaries are enough to discard with. For each case that survives, fetch the
full case before you cite it:

```
GET https://mistake.club/api/archive/case/<id>
authorization: mc_skill_<token>
```

That returns the whole entry — `summary`, `why`, `cost`, `trigger`, `lesson`,
`sources` and the rest of the archive's schema. If it answers
`403 {"error":"full_archive_only"}`, the case sits outside the member's tier:
leave it out of the deliverable. Never argue from a summary alone.

### 4. Read — the step that separates a consultant from a card catalogue

Selection is not keep-or-drop against a headline. **A case is not its
headline**: `why[]` usually holds several distinct mechanisms, and the strand
that speaks to this plan is often not the famous one. Kodak is filed as
"buried its own invention" — but for a plan defending a high-margin legacy
line, the applicable strand is the margin structure that made burying feel
rational. Read `why[]` and `lesson`, and name **which strand answers which
risk**. Cite the case for the strand, not the fable.

Three reads, in order, for every fetched case:

- **Map it to an axis.** Which of the step-1 axes does this case actually
  speak to — demand, unit economics, timing, integration, people, regulation,
  channel, brand? A case that maps to none of your risks is trivia,
  however famous.
- **Build the bridge when the industry differs.** Cross-industry is not a
  weakness to apologise for; it is where the sharpest precedents live, because
  the mechanism is visible without the industry's costume. But the bridge must
  be said in one sentence — *different industry, same joint: a fixed-cost
  commitment made before the demand signal existed* — or the reader dismisses
  the case as someone else's problem.
- **Let one risk take two lenses.** When two cases illuminate the same
  risk from different dimensions — one shows the demand misread, the other
  shows what the cost structure did when demand missed — keep both and say
  what each contributes. That is a richer answer, not a longer list.

Then drop everything else. Two to four **precedent blocks** in the
deliverable — a block is one risk plus the case or pair of cases read
against it. If two cases say the same thing about the same risk, keep the
better documented one. Ask of every survivor: *would this failure have
happened to this plan, for this reason?* If no, out.

### 5. Write it into the plan, not into an appendix

For each surviving precedent, one block:

- **The risk** — which decision in the plan this is about.
- **Who already did it** — company, year, one line on what they did.
- **What it cost** — the figure from `cost`, quoted, not rounded up.
- **The strand that applies** — from `why[]`, in your own words: the specific
  mechanism this plan is exposed to, and — when the industry differs — the
  one-sentence bridge that carries it across.
- **What this plan does differently** — or, if it doesn't, say that plainly.
  This line is the whole point; without it the precedent is trivia.
- **Source** — the case's `sources`, so the reader can check you.

Then, if several survivors share a decision type or category, add one short
paragraph naming the shared mechanism across those cases. That paragraph is
the analysis; the cases are the evidence for it.

Then close with **What to change before this ships** — two or three numbered
imperatives, each traceable to a precedent above, each naming the smallest
thing that would make this plan not that case: a pilot before the rollout, a
kill date on the discount, a named owner for the integration. This list is
what the reader came for; the precedents are why they should believe it. Keep
each item to one sentence, and never list a change no precedent argues for.

Finish with what you did **not** find: the angles that returned nothing.
Silence in the archive is not safety — say so in those words.

The full output template and a worked example are in `reference.md`. Read it
the first time you run a cross-check.

## Rules

- **The reader never sees the machinery.** Field names, endpoint paths and
  parameter syntax — `why[]`, `q=`, `/api/archive/case/` — are how you talk to
  the archive, never how you talk to the human. In the deliverable (and in any
  narration the human reads), translate them: "its root causes", "I searched
  from three angles". An answer that quotes its own plumbing reads like a
  printout, not a consultant.
- **Never invent a case, a number or a URL.** Everything you assert comes from
  a returned field. If you want to say something the archive does not support,
  attribute it to yourself, not to the archive.
- **Quote `cost` and `era` verbatim.** They are the facts most likely to be
  misremembered.
- **Verify before it ships.** The archive is human-reviewed but not infallible;
  open the case's `sources` for any figure that will appear in a client
  deliverable.
- **Nothing found ≠ nothing exists.** Say "no precedent in the archive for X",
  never "no one has failed at this". And a core-tier search covers the core
  archive — say "no precedent in the core archive" when that is what you
  searched.
- **A case cited for its famous story is a near-miss in disguise.** The test
  of a real read is that you can name the strand, the axis it maps to, and the
  risk it answers — in one line each. If you can only retell the story,
  you have not read it yet.
- **Angles too broad is the failure mode to expect.** If your hits read like
  a list of famous disasters rather than your plan's relatives, the archive is
  not empty — your nouns are. Sharpen once and re-ask.
- **Do not moralise.** These are decisions that looked rational at the time.
  Explain the mechanism; don't call anyone stupid.
- **Quiet by design.** The only thing that leaves the machine is the search
  terms you chose, sent under the member's token. The archive never sees the
  document, and you never send it.

## The community compact

The archive grows the way open source grows: everyone gets the core, and the
people who help build it get more of it.

- **The core archive — 5,000 documented failures — is free for every member,
  forever.** No contribution is ever required to keep it, and this skill works
  in full against it.
- **The full archive — 6,000+ cases and growing — opens for members who
  contribute.** Fifty approved cases and it opens for good.
- **Every approved case is credited `community:@yourhandle`** and shows on the
  member's profile contribution wall. The work carries a name.

There is no pressure in either direction. A member who never contributes loses
nothing they already have.

## Contributing — a separate session, by design

Contributing is its own kind of work — research, not consulting — so it never
happens inside a strategy session. When the human asks to contribute, run it
as a dedicated session, and if a cross-check session ends with the member
close to the unlock, mentioning it once is enough.

The protocol:

1. **Fetch the assignment slots:**
   `GET /api/contribute/slots` — the market × industry gaps the archive most
   needs filled. Work a slot; do not freestyle duplicates of famous cases.
2. **Research real, documented failures** for the slot. Every case must be a
   thing that actually happened to a named organisation.
3. **Verify every source.** Fetch each URL and confirm it says what the entry
   claims it says. Never invent a URL, a name or a number — a plausible source
   that says the wrong thing is worse than none.
4. **Submit drafts:** `POST /api/contribute/submit` with
   `{"entries": [<entry JSON>]}` — entries in the archive's schema (`summary`,
   `why`, `cost`, `trigger`, `lesson`, `sources`, …).
5. **A human curator reviews each one.** Drafts land in a review queue, never
   straight into the archive. Approved cases go live credited
   `community:@yourhandle`; rejected ones come back with a note saying why and
   what to fix.

`GET /api/skill/status` reports the member's standing — `approved`, `pending`,
and `remaining` against `unlockAt` — so the session can end with an honest
count.

## The end-of-run ritual

Every run of this skill ends the same way, however short the run:

1. Call `GET /api/skill/status` with the token header.
2. Relay its `message` to the human, in your own words.

`message` is a ready-to-relay English sentence about the member's access —
their tier, their progress toward the full archive, whatever the member should
hear next. Do not skip it because the run was small, do not bury it in
machinery, and do not editorialize it into a sales pitch — just make sure the
human hears it.
