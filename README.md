# The Oracle Stream — public forecast ledger

Raw, machine-readable ledger of every prediction made by the AI on
**[The Oracle Stream](https://www.youtube.com/@TheOracleStream1/live)** — a 24/7
livestream where an open-source forecasting engine predicts world events and
grades itself on air.

**This repo is the notary.** Every forecast is committed here shortly after it
is published on the stream, *before* its outcome is known. The commit history
provides independent timestamps: we cannot edit a prediction after the fact
without it showing in the git log. Misses stay in the ledger forever — that's
the point.

## How it's doing (as of 2026-07-27, 673 resolved forecasts)

Badly, on the headline numbers, and we'd rather you hear it here:

| | The oracle | A rock that always says "no" |
|---|---|---|
| Hit rate | 61% | **93%** |
| Brier score | 0.226 | **0.070** |

Only **7.0%** of resolved forecasts actually came true, so the trivial
always-say-no baseline beats the oracle on both headline metrics. Of the 275
forecasts it made at ≥50% confidence, 29 happened.

It is not pure noise, though. The *ranking* carries information — higher-probability
forecasts do come true more often (AUC 0.639, calibration bins monotone) — and a
Platt calibration fit on its own history, walk-forward, cuts Brier 0.227 → 0.164
across 443 held-out resolutions. The honest summary is **weak but real signal in
the ordering, badly inflated confidence numbers.** When it says 50%, the observed
rate is about 6%.

The published score stays raw rather than calibrated, because the calibrated
number is fit on the same history it would be scored against. Live figures are in
`scorecard.json`; this table is a stamped snapshot and will drift from it.

## Contents

| File | What it is |
|---|---|
| `ledger/forecasts.jsonl` | Every forecast: statement, probability, horizon, reasoning, timestamp (`ts`, epoch ms), resolve-after deadline |
| `ledger/resolutions.jsonl` | Every resolution: verdict (yes/no), outcome score, the judge's evidence, resolution timestamp |
| `scorecard.json` | Current aggregate accuracy: hit rate, Brier score, per-horizon stats, calibration bins |
| `receipts/` | Auto-generated, human-vetted "receipt" cards for resolved-correct forecasts |
| `audits/` | Write-ups of every judge failure found, with what was voided and what was fixed |

Join records across files on `id` (e.g. `pred_20fed9784b`).

## The audits

An automated judge grades the forecasts, and it has been wrong in ways that
inflated the record. Each time, the affected resolutions were voided — appended
as superseding records carrying `revised: true`, never rewritten — and the
mechanism written up. **74 resolutions currently carry `revised: true`.** The
corrected scorecard shows only the answer; these show the working.

| Audit | What went wrong | Voided |
|---|---|---|
| [2026-07-18](audits/2026-07-18-ledger-audit.md) | The judge resolved forecasts by citing irrelevant evidence — a Fed-rate forecast resolved YES on a Mexican earthquake, hurricane forecasts resolved by an unrelated cyclone. All 95 resolutions to date audited one by one. | 43 |
| [2026-07-20](audits/2026-07-20-premature-dated-resolutions.md) | Forecasts that date their own event past their horizon ("…at the FOMC meeting on July 28th", written at a 24h horizon) were auto-graded NO when the window closed — days before the event. | 8 |
| [2026-07-22](audits/2026-07-22-premature-scheduled-resolutions.md) | The same defect for fixtures named *without* a date ("the EIA weekly petroleum status report"), which the date parser couldn't see. | 13 |
| [2026-07-22](audits/2026-07-22-chokepoint-conflict-blindness.md) | A forecast-generation defect, not a grading one: the oracle read a collapsing shipping chokepoint as a mean-reversion buying opportunity, because the live conflict feed was being silently dropped at intake. Nothing had resolved, so nothing was voided. | 0 |
| [2026-07-27](audits/2026-07-27-brain-causal-gates.md) | The learned event→market layer was surfacing couplings with no transmission mechanism (and some with backwards signs). Gated; 12 surfaced connections dropped to 3. | 0 |

## Honest caveats

- The engine is young. 673 of 2,551 forecasts have resolved, all of them at the
  24h and week horizons; month and year horizons have not come due.
- The judge is an LLM. It is a stronger model than the forecaster, it must cite a
  relevance-vetted world signal from the forecast window verbatim, and every
  resolution records which model graded it — but an LLM grading an LLM is a real
  limitation, and the audits above are what we have instead of a solution.
- Corrections are appended, not rewritten: a voided resolution is superseded by a
  record with `revised: true`, and the original verdict is preserved inside it.
  The git history shows the correction rather than a rewrite.
- Data only. The stream's broadcast system and learning layers are separate,
  private code.

## Credits

Forecasting engine: [PYTHIA](https://github.com/jangles-byte/Pythia)
(open source — built by its own authors; this project runs and extends it).
Stream, infrastructure, ledger tooling: the Oracle Stream operator.

Updated automatically, roughly hourly.
