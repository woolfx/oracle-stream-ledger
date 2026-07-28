# Forecast audit — premature resolution of scheduled-fixture forecasts (INC-017)

2026-07-22. Found during the INC-016 "where else are we blind?" sweep, by reading
the live scorecard's calibration table rather than any single card.

## Symptom

Calibration was badly inverted — the oracle's high-confidence bins barely beat its
low ones:

| predicted | observed | n |
|---|---|---|
| 82% | 17% | 24 |
| 67% | 16% | 64 |
| 49% |  9% | 80 |

A chunk of the "overconfidence" was an artifact: forecasts resolving **NO** on
events that **had not happened yet**. Recent resolutions, all graded NO the same day:

> *"The EIA weekly petroleum status report will move CL=F by +2.5%"* — p=0.75 →
> **no**: *"scheduled for 2026-07-29, after the window closed on 2026-07-22."*

The oracle drafts **24h** forecasts about the **EIA petroleum report (due 07-29)**;
the judge meets them on the 22nd, sees the report hasn't occurred, and grades NO.
Guaranteed-wrong resolutions on the public falsifiability ledger.

## Mechanism (a gap INC-014 explicitly predicted)

INC-014 fixed date-*pinned* statements ("…FOMC on July 28th") via
`relevance.event_end_ms()` — but that parser only reads **explicit dates in the
statement text**. A statement that names a scheduled fixture **without a date**
("the EIA weekly petroleum status report", "the next FOMC meeting") returns
`None` → no horizon bump → 24h window → forced NO. CLAUDE.md's INC-014 entry
warned verbatim: *"If oracle drafts ever date events IMPLICITLY ('this month's
FOMC'), extend the parser first."* This is that case, live.

Ledger scan (`Pythia/runs/ledger.jsonl`, non-revised verdicts): **22 premature
scheduled NOs** still counting in the scorecard — **13 EIA report, 5 FOMC, 4
other** — 9% of all NO verdicts, and still being minted (4 in the last dozen).

## Fix (engine, ships with this audit — pending restart)

1. **`relevance.py · scheduled_end_ms()` (new)** — companion to `event_end_ms`.
   Given the live `[SCHEDULED]` fixtures `(title, end_of_day_ms)`, returns the
   **next** fixture a statement names. A SPECIFIC match (≥2 shared content tokens,
   generic scheduling words like *report/weekly/meeting* excluded) cleanly
   separates the petroleum report from the natural-gas one; failing that, a shared
   acronym anchor (EIA/FOMC/…) matches the fixture type; among matches the soonest
   future occurrence wins ("the next FOMC").
2. **`relevance.py · scheduled_fixtures()`** — shared helper turning `[SCHEDULED]`
   world events into `(title, end_of_day_ms)`, used by both guards below.
3. **`pipeline.py` (commit guard)** — bumps horizon with `event_end_ms(...) or
   scheduled_end_ms(...)`, so a new EIA-report forecast becomes month-horizon
   (window reaches past 07-29) instead of 24h.
4. **`loop.py · resolve_due` (resolution guard)** — defers grading with the same
   `event_end_ms(...) or scheduled_end_ms(...)` (fixtures from `STATE.events`).
   This is the DUAL guard INC-014 uses (commit + resolution): #3 protects forecasts
   made after deploy, #4 also covers ones **already committed at 24h** before it,
   so the pre-existing backlog stops resolving early instead of dribbling out more
   bad NOs. Verified: a due 24h EIA forecast defers to the report day; normal
   market forecasts resolve on time.

Verified: petroleum↔gas disambiguation, next-FOMC selection, normal market
forecasts untouched (stay 24h), and against the live calendar feed a real
EIA-petroleum forecast bumps 24h→month (window reaches 2026-08-21, report is
07-29 → no premature NO).

## Done 2026-07-27 — 13 premature NOs voided

Harvey approved the ledger operation. Re-scanned the ledger first, because the
"22" above was counted on 07-22 with a looser filter and the population had since
moved. What is actually in the record now:

| class | count | disposition |
|---|---|---|
| EIA weekly petroleum status report → CL=F, graded NO before the report existed | **13** | **voided 2026-07-27 19:25 EDT** |
| FOMC / Federal Reserve | 0 still counting | 9 already carried `revised: true` (INC-014's 8 + 1) |
| tropical cyclone / hurricane / tornado "no landfall" NOs | 4 | **kept** — they only matched the old scan's "after the window closed" phrase; no scheduled fixture is involved and the events genuinely didn't happen |
| NG=F "…following the EIA storage report on July 23" | 3 | **kept** — date-pinned, the report *did* fall inside the window, and one cites Henry Hub down 0.62 %: real MISSes, not premature ones |

Each of the 13 was verified by the judge's own words admitting the defect, e.g.
*"The EIA weekly petroleum status report is scheduled for 2026-07-29, seven days
after the forecast window closed on 2026-07-22."* The forward guard is holding:
**zero** new premature scheduled NOs have been minted since the 07-22 fix.

Method (same shape as INC-014 — append, never rewrite): a superseding resolution
per id with `verdict: "unresolved"`, `outcome: null`, `revised: true`,
`judge: "inc-017-audit"` and the original verdict preserved under `superseded`.
The ledger loads last-wins, so the forecast leaves the Brier/hit pool and lands in
`unresolvable`; the public mirror's git history shows the correction rather than a
rewrite. The operation now has a reusable, dry-run-by-default tool —
`infra/void-resolutions.py` — which is idempotent (re-running skips voided ids).

**Scorecard: 683 resolved / 60.2 % hit / 0.2309 Brier → 670 / 60.9 % / 0.2262**
(unresolvable 51 → 64). The engine serves its in-memory copy until it restarts, so
the on-air ORACLE RECORD and `/scorecard` show the old numbers until then; the
hourly mirror exports the corrected `resolutions.jsonl` immediately.

Also noted while scanning: **2 of 3,639 ledger lines are unparseable** — both from
launch week (llama3.1 era), each a run of NUL bytes followed by a valid JSON tail,
the classic ext4 delayed-allocation artifact of a crash during append (INC-001/002
era reboots). Two forecast records lost, unrecoverable, no live impact.

## Route-failure blindness (folded in, same sweep)

`osiris_intake._fetch_feed` logged route errors/timeouts at `debug` — a whole
domain could go dark invisibly (`flights` is doing this now, ReadTimeout every
pass). Now `warning` on both a non-200 status and an exception. Complements the
INC-016 mis-parse warning: parse-drops AND route-drops now both self-announce.

## Divergence note

Engine files — `relevance.py`, `pipeline.py`, `osiris_intake.py` all already on
the CLAUDE.md "diverges from upstream" list. No overlay copy-back.
