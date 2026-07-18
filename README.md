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

## Contents

| File | What it is |
|---|---|
| `ledger/forecasts.jsonl` | Every forecast: statement, probability, horizon, reasoning, timestamp (`ts`, epoch ms), resolve-after deadline |
| `ledger/resolutions.jsonl` | Every resolution: verdict (yes/no), outcome score, the judge's evidence, resolution timestamp |
| `scorecard.json` | Current aggregate accuracy: hit rate, Brier score, per-horizon stats, calibration bins |
| `receipts/` | Auto-generated, human-vetted "receipt" cards for resolved-correct forecasts |

Join records across files on `id` (e.g. `pred_20fed9784b`).

## Honest caveats

- The engine is young; most long horizons have not resolved yet. Current
  aggregate stats are in `scorecard.json` — flattering or not.
- Resolutions come from an automated judge and are periodically audited;
  entries found mis-resolved are corrected in the source ledger and the
  correction is visible here as a diff (append-only in spirit, auditable in
  practice).
- Data only. The stream's broadcast system and learning layers are separate,
  private code.

## Credits

Forecasting engine: [PYTHIA](https://github.com/jangles-byte/Pythia)
(open source — built by its own authors; this project runs and extends it).
Stream, infrastructure, ledger tooling: the Oracle Stream operator.

Updated automatically, roughly hourly.
