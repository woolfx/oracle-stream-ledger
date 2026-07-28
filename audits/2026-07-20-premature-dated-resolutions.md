# Ledger audit — premature resolutions of date-pinned statements (INC-014)

2026-07-20. Trigger: Harvey saw "✗ MISS — The Federal Reserve will raise
interest rates at the next FOMC meeting on July 28th · called 80% · it didn't
happen" in the on-air ORACLE RECORD rotator — eight days before July 28.

## Mechanism

The deck re-drafts a hot statement every oracle pass; the [SCHEDULED] calendar
feed (live since 07-18) pins statements to exact dates (FOMC 2026-07-28). Each
FOMC draft carried a **24h horizon**, so each expired ~a day later and hit the
judge, whose INC-010-hardened rule — "window closed, no confirming signal →
no" — is blind to the statement dating its own event beyond the window. Every
verdict below was cast by the Tier-2 judge (claude-opus-4-8) and each evidence
sentence *admits the defect* ("…meeting is scheduled for 2026-07-28, after the
window closed…"). One bogus public MISS rolled off the line per pass.

## Voided records (superseding `revised: true` resolutions appended 2026-07-20 ~15:40 EDT)

| id | statement (trimmed) | called | horizon | graded | by |
|---|---|---|---|---|---|
| pred_f549d5b574 | Fed will raise rates at their meeting on July 28th | 47% | 24h | 07-19 16:59 no | opus-4-8 |
| pred_7416af7c58 | FOMC will raise ≥50bps during the July 28 meeting | 59% | 24h | 07-19 22:31 no | opus-4-8 |
| pred_b4a71735dd | FOMC will raise rates at the July 28 meeting | 80% | 24h | 07-20 03:02 no | opus-4-8 |
| pred_ec78991397 | Fed will raise rates at their next meeting on July 28th | 84% | 24h | 07-20 04:03 no | opus-4-8 |
| pred_5a34ec363a | Fed will raise rates at the next FOMC meeting on July 28th | 85% | 24h | 07-20 05:03 no | opus-4-8 |
| pred_a83b39ba9f | Fed will raise rates at the upcoming FOMC meeting on July 28th | 80% | 24h | 07-20 11:04 no | opus-4-8 |
| pred_e69b8e9447 | FOMC meeting on July 28 will result in a rate hike | 78% | 24h | 07-20 12:04 no | opus-4-8 |
| pred_3194a4fd27 | Fed will raise rates at the next FOMC meeting on July 28th | 80% | 24h | 07-20 14:04 no | opus-4-8 |

Kept: pred_f0db3ef5f9 ("severe thunderstorm warning issued for the Midwest on
July 18", YES, graded 07-18 21:56) — flagged by the same date scan but the
verdict is a YES **on the event day after the event occurred**; premature-NO
logic does not apply.

Scorecard: 178 resolved / 52% hit / 0.280 Brier → **170 / 54% / 0.269**,
unresolvable 43 → 51. The public ledger mirror carries the revision records on
its next hourly push — the notary history shows the correction, not a rewrite.

## Open forecasts saved by the same fix (18 at audit time)

Date-pinned statements whose horizon ends before their event date (FOMC July
28 ×6 more, September 15th FOMC, Paris flood July 25th, Mexico hurricane
August 15, Brent $92 by August 31, …) — the resolve-loop now **defers** them
(no verdict until the named day ends) instead of grading them "no" at window
close; two were due to be MISSed the same evening (19:28 and 23:05 EDT).

## Fixes (engine, shipped with this audit)

1. `relevance.py · event_end_ms()` — deterministic explicit-date parser
   (month-name + day [+year], ISO dates; year rolls forward when a yearless
   date lands >60 days behind the forecast).
2. `loop.py · resolve_due()` — dated statements are deferred until their day
   ends (judged afterward with the evidence window extended to cover the event
   day); the grace-void also waits until event-day +2d.
3. `pipeline.py` — at commit, a dated statement's horizon is bumped to the
   smallest window that contains its date (the July 28 drafts become `month`,
   not `24h`), logged per bump.
