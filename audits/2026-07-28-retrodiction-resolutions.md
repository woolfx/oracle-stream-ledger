# Ledger audit — resolutions citing evidence that predates the forecast (INC-020)

2026-07-28. Trigger: a routine pre-posting review of the receipt backlog. Checking
whether any shareable receipt rested on a superseded resolution turned up something
worse — a receipt card, cut and ready to post, for a forecast whose cited evidence
had happened **before the forecast was written**.

## Mechanism

`engine/relevance.py::citable()` is the INC-010 contract: a "yes" must cite a
numbered signal, and that signal must match the claim's subject, place, and any
stated magnitude threshold. It checks *what* the signal says. It never checked
*when* the signal happened.

GDACS disaster lines carry their own event timestamp and stay in the world brief
for days after the event. The judge's candidate pool includes `[now]` lines — a
snapshot of the current brief, there to catch aftermath. So a days-old event could
sit in the brief, be drafted into a forecast by the oracle that was *looking at it*,
and then be cited back as proof that the forecast came true. Every INC-010 guardrail
passes: same subject, same place, threshold met. Only the ordering is wrong, and
nothing was checking ordering.

The result is not a wrong verdict in the ordinary sense. The events are real, the
citations are accurate, the arithmetic is right. The forecasts simply were not
forecasts.

## Voided records (superseding `revised: true` resolutions appended 2026-07-28)

Both cite the same signal — the GDACS M7.3 in Mexico, **2026-07-17 14:48 UTC**.

| id | statement (trimmed) | called | horizon | published (UTC) | lag vs event | graded |
|---|---|---|---|---|---|---|
| pred_33040ed18b | A major earthquake will strike Mexico within the next fortnight | 69% | 24h | 07-17 17:32 | **+2.7 h after** | 07-18 17:42 yes |
| pred_a5d40d75c5 | An earthquake with magnitude greater than 7.0 will occur in Mexico within the next 24 hours | 20% | 24h | 07-17 19:04 | **+4.3 h after** | 07-18 19:42 yes |

Both were graded by the local judge. `pred_33040ed18b` had a receipt card cut from
it (`20260718-1342_p69_…`), now quarantined; quarantined receipts are never exported.

Scorecard: 695 resolved / 61.2% hit / 0.2255 Brier → **693 / 61.2% / 0.2251**,
unresolvable 64 → 66. The correction is deliberately reported as small: two records
in 695 move the headline by less than a tenth of a point. The reason to publish this
is the defect class, not the delta.

## How far it goes — stated honestly

A scan of resolved-correct records looked for evidence text stating an absolute
event time that could be compared against the forecast's publication time:

- 100 resolved-correct records scanned
- **7** state an absolute, unambiguously parseable timestamp
- **2** of those 7 predate their forecast — the two voided above

**7 of 100 is far too small a sample to infer a rate**, and it is not a random one:
GDACS lines carry explicit timestamps, while NWS product lines and market snapshots
— the bulk of the evidence pool — state no absolute time and are therefore
unmeasurable by this method. **2 is a floor, not an estimate.** The true count is
unknown and this audit does not claim otherwise. Anyone reading the published hit
rate should treat it as carrying an unquantified retrodiction component, bounded
below by 2 and above by nothing established here.

## Fix

`engine/relevance.py` gains `signal_event_ms()` and a `made_ms` parameter on
`filter_signals()`. Signals whose own text dates their event before the forecast's
publication are dropped from the candidate pool, so the judge is never offered them
— retrodiction is blocked at the source rather than patched after the fact.
`engine/oracle.py::judge()` passes `made_ms=forecast["ts"]`.

The parser is conservative by construction. It reads only unambiguous,
fully-specified timestamps; a day/month pair that could be read either way returns
None rather than guessing, and an unparseable signal stays **admissible**. Silence
is not treated as guilt — the common case states no absolute time, and dropping
those would starve the judge and collapse every verdict to "no". This narrows the
defect to the class that can be proven; it does not eliminate it.

`infra/receipts.py` carries the same gate independently, so a retrodiction can
never reach a shareable card even if one is graded correct.

Tests: `Pythia/tests/test_retrodiction.py` — 17 cases, mutation-verified ×5
(disabling the drop, inverting it to treat unparseable as guilty, removing the
ambiguity refusal, breaking AM/PM, and flipping the comparison each trip failures).
The live record above is pinned as a regression test.

Takes effect on the next `pythia-engine` restart; resolutions already recorded are
unaffected except for the two voided here.

## What this does not fix

- Evidence *after* the window closes is still unchecked; this guard only looks at
  the leading edge. INC-014 and INC-017 cover the dated/scheduled cases at the
  trailing edge, but the general case is open.
- Signals stating no absolute time remain unorderable, which is most of them.
- The oracle drafting forecasts about events already in its own brief is upstream
  of the judge and is not addressed here. This audit closes the grading hole, not
  the drafting one.
