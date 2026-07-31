# Output template and a worked example

Read this the first time you run a cross-check. The shape matters: a reader
must be able to check every claim, and see instantly what the plan should
change.

---

## The template

```markdown
## Precedent check — N exposures, M precedents

### 1. <the exposure, in the plan's own words>
**<Company> · <era> · <one line on what they did>**
It cost them: <the `cost` field, verbatim>
Why it failed: <the mechanism, from `why[]`, in your words — two sentences>
**This plan:** <what the plan does differently — or, honestly, that it doesn't>
Source: <url>

### 2. …

### The pattern
<Only when `repeats` shows the same decision type or category twice or more.
One paragraph: the mechanism these failures share, and what that implies for
this plan. This is the analysis; everything above is the evidence.>

### What to change before this ships
<Two or three numbered imperatives, each traceable to a precedent above, each
the smallest thing that makes this plan not that case. One sentence each.
This list is what the reader came for; everything above is why to believe it.>

### Checked, nothing found
<Angles that returned no case. One line: "the archive has no precedent for X —
that is an absence of evidence, not evidence of safety.">
```

Rules of the shape:

- **Two to four precedents.** More reads as a literature review and gets skipped.
- **Every block ends with "This plan:".** A precedent with no consequence for
  the document in front of you should not be in the document.
- **`cost` and `era` are quoted, never paraphrased upward.** "$9B+ losses;
  Westinghouse bankrupt" — not "roughly ten billion".
- **The pattern paragraph is optional.** Two unrelated failures are not a
  pattern; do not manufacture one.

---

## A worked example

**The plan:** open 12 big-box home-improvement stores in tier-1 Chinese cities
within 18 months.

**Exposures named:** a retail format transplanted into a different consumer
culture · heavy fixed cost committed before demand is proven · a foreign brand
carrying cultural risk in China.

**The call:**

```
GET /api/big-mistakes/search
  ?q=china+market+entry&q=big-box+retail&q=store+openings+fixed+cost
  &detail=full
```

**What came back** (abridged): `home-depot-china`, `uniqlo-us-struggles`,
`dolce-gabbana-china-chopsticks`, `kfc-china-yum-spinoff`, `toshiba-nuclear-bet`,
`lotte-china-thaad`, plus `repeats.byDecision: strategic ×8`.

**What was dropped and why:** `toshiba-nuclear-bet` (same *kind* of bet — big
capital commitment on a forecast — but the mechanism is an energy-policy
reversal, not consumer behaviour); `kfc-china-yum-spinoff` (a corporate
structure decision, not a market-entry one); `ofo` (a funding-model collapse).
Industry adjacency is not a precedent.

**The deliverable:**

```markdown
## Precedent check — 3 exposures, 3 precedents

### 1. The format assumes DIY demand that may not exist
**The Home Depot · 2006–2012 · exported its US big-box DIY format to China**
It cost them: all big-box stores closed by 2012
Why it failed: the format assumed customers who renovate their own homes, in a
market where hiring a decorator is the norm — so the aisles that make the model
work in the US were the aisles nobody in China needed.
**This plan:** assumes the same DIY behaviour in section 2 and cites no local
demand data. Before signing 12 leases, validate with one leased pilot store and
a survey of renovation spend; move the remaining 11 behind that gate.
Source: https://mistake.club/w/home-depot-china

### 2. A brand that travels well at home may not travel here
**Fast Retailing (Uniqlo) · 2019 · 60 US stores in a decade**
It cost them: a decade of slow US growth; missed global #1 target
Why it failed: an aesthetic that carried the brand across Asia — minimalist,
logo-free — read as anonymous to American shoppers who buy brand signals. The
product was right; the meaning of the product was not.
**This plan:** treats brand recognition as portable. Budget for local brand
building rather than assuming the name arrives ahead of the stores.
Source: https://mistake.club/w/uniqlo-us-struggles

### 3. Cultural risk in China is a business risk, not a PR footnote
**Dolce & Gabbana · 2018 · an ad campaign built on a national stereotype**
It cost them: China market; cancelled show; retailer delistings
Why it failed: the campaign was signed off by people with no authority to say
"this reads as contempt here", and the reaction removed the brand from its
largest growth market in days.
**This plan:** has no local sign-off step for marketing. Give a China-based
reviewer veto over campaign creative before it runs.
Source: https://mistake.club/w/dolce-gabbana-china-chopsticks

### The pattern
All three failed at the same joint: a decision made where the head office
understands the customer, executed where it doesn't. None of them failed on
capital, logistics or product quality. The plan's risk register lists supply
chain and capex; it does not list "our read of the customer is wrong", which is
the exposure the archive keeps returning.

### Checked, nothing found
No precedent in the archive for tier-1-city lease structures specifically. That
is an absence of evidence, not evidence of safety.
```

Note what the example does **not** do: it does not summarise all six returned
cases, it does not call anyone foolish, and it does not round "all big-box
stores closed by 2012" into "a total failure". Each block ends with an
instruction the plan's author can act on this afternoon.
