# Judge audit — web search in the resolution path

2026-07-28. Not triggered by a new incident. Triggered by noticing that INC-010,
INC-014 and INC-017 are **the same bug three times**, and that the guard shipped
for each one only narrowed the blast radius rather than removing the cause.

## The cause the three incidents share

The judge can only cite evidence that PYTHIA's own feeds captured. Its operative
rule is therefore:

> "the window closed and no captured signal shows the event → **no**"

That rule silently converts *absence of evidence in our feeds* into *evidence of
absence*, and writes the result into a git-notarised public ledger as a MISS.

| incident | date | class | voided |
|---|---|---|---|
| INC-010 | 2026-07-18 | judge cited irrelevant signals (Mexico earthquake → Fed forecast) | 43 |
| INC-014 | 2026-07-20 | statement dated its own event past the window | 8 |
| INC-017 | 2026-07-22 | statement named an undated scheduled fixture | 13 |

Each was caught **by eye, months apart**, and each fix addressed a *route* into
the failure (relevance vetting; explicit-date parsing; fixture matching) rather
than the blind spot itself. A fourth route was always going to exist: a real
event that simply never appeared in our feeds grades "no" with no tell.

## The change

`engine/websearch.py` (new). Before the verdict is drafted, the judge runs one
web search call and folds the results into its candidate evidence pool.

The design constraint was to widen **what is citable** without touching **how
citation works**:

```
gather() → [web] lines → candidates → relevance.filter_signals()
                                    → numbered S1..Sn in the prompt
                                    → judge cites BY NUMBER
                                    → relevance.citable() re-checks
                                    → verbatim text stored in the ledger
```

Nothing downstream of `candidates` knows a signal came from the web. The judge
still cannot paraphrase a claim into evidence; a "yes" still needs a citation
that survives the place/topic/threshold veto; the ledger still stores
third-party text verbatim. The INC-010 contract is unchanged, and now covers a
strictly larger evidence surface.

### What a web citation looks like

`web_search_result` blocks expose `title`, `url` and `page_age`; the readable
snippet is encrypted and legible only to the model (measured against the live
API, 2026-07-28). So a citation is a dated headline from a named domain — the
same shape as the news lines PYTHIA already ingests, plus a URL. That makes a
`[web]` citation in the public ledger **more** checkable by a reader than a bare
feed line, not less: the link is right there.

```
[web] July 2025 Fed Meeting: Rates Hold Steady Once More … | Chase
      (chase.com, July 31, 2025) https://www.chase.com/personal/investments/…
```

## Three decisions worth recording

**Search runs on every Tier-2 verdict, not only the ones heading for "no".**
Gathering evidence only when the judge is about to say "no" would convert
no→yes and never yes→no — a one-way ratchet that inflates the hit rate in
exactly the direction that flatters the project. Symmetric or not at all.

**The searching model's prose is discarded entirely.** Only the structured
result blocks are harvested. The model chooses what to search; it does not get
to narrate, so there is no path from its own words into the ledger.

**Result titles are treated as untrusted input.** A title is attacker-
influenceable text landing in a numbered `S1.` evidence list, so a newline plus
`S9. <fabricated>` would forge a signal the judge could then cite by number —
the one move that walks straight through the whole contract. Titles are
flattened, control characters stripped, non-http URLs rejected, length capped.

## Verification

Two forecasts, both with a deliberately **empty** local evidence pool — the
exact shape that used to auto-grade "no":

| forecast | reality | verdict | citation |
|---|---|---|---|
| Fed leaves rates unchanged at the July 2025 FOMC meeting | true | **yes** | Chase, July 31 2025 headline |
| Fed cuts rates 50bp at the July 2025 FOMC meeting | false | **no** | — |

The first would have been a false MISS on the public ledger under the old judge.
The second confirms search is not a rubber stamp.

`Pythia/tests/test_websearch.py` — 22 tests, mutation-verified: defanging the
title sanitiser, the `page_age` handling, the URL scheme check, the error-block
logging, the evidence-pool cap widening, and the `gather()` call site each turn
the suite red.

## Honest limits

- **A headline is not an article.** The judge is told to grade `[web]` lines on
  what the headline itself states and to check the publication date, but a
  misleading headline is a misleading citation. This trades one failure mode
  (blind to reality) for a smaller one (misread a headline).
- **Short headlines can fail the relevance gate.** `related()` falls back to a
  ≥3-token overlap, which a terse headline may not clear. It errs toward
  exclusion — the safe direction — but it does mean search recall is imperfect.
- **No skill claim.** This changes how outcomes are *graded*, not how forecasts
  are *made*. It should make the scorecard more accurate, in whichever direction
  accuracy lies. It is not expected to, and should not be read as, an
  improvement in PYTHIA's forecasting ability.
- **Instrument change mid-record.** Resolutions before and after today were
  graded by different judges. Every resolution now carries `judge_search` (how
  many web signals it had available), so the boundary is a partitionable field
  rather than an invisible discontinuity. Prior resolutions have no such field.
