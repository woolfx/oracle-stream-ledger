# Forecast audit — chokepoint mean-reversion blind to conflict (INC-016)

2026-07-22. Trigger: Harvey saw the on-air PYTHIA FORECAST card *"The Strait of
Hormuz will experience a significant increase in ship transits, exceeding its
30-day average by at least 20% within the next week"* (48%) — while a shooting
war in the Gulf was, per Harvey, shutting the strait. The forecast points the
arrow **up** (traffic rebounds) at the exact moment physical flow is collapsing
toward zero.

**Outcome (live, 13:01 EDT deploy).** On the first oracle pass after deploy the
Hormuz forecast redrafted and the arrow flipped: `pred_ac7d3c4962` *"…significant
**increase** … exceeding its average by ≥20%"* (48%) → `pred_acfbafea41` *"Strait
of Hormuz will see a **decrease** in ship transits by 20% in the next week"*
(37%), reasoning now *"sustained deviation below its 30-day average, indicating
physical evidence of trade disruption."* Note the marker did **not** fire for
Hormuz (the actual Gulf war is still absent from the ingested feeds — see Open
gap); the prompt hardening alone corrected the direction. The proximity gate is
armed for when a geocoded Gulf conflict signal arrives. (Aside: the redrafted
forecast carries the LLM's own `lat:-26.6` — wrong hemisphere for Hormuz; a
pre-existing coordinate-hallucination quirk unrelated to this fix, worth a
separate glance since it misplots the globe pin.)

This is a **forecast-generation** defect caught at draft time, not a resolution
defect: nothing had resolved, so — unlike INC-010 / INC-014 — **no ledger
records are voided and the scorecard is unchanged**. The bad forecast was live
on the broadcast but never graded.

## The forecast (pred_ac7d3c4962, brief_f7df0a5b78)

- statement: Strait of Hormuz transits will exceed the 30-day average by ≥20% within a week
- probability **0.48** (base 0.60, prev 0.40, `split: true`), horizon `week`, @26.5,56.3
- reasoning (verbatim): *"Current deviation of 30% below its 30-day average indicates potential for increased trade activity"*
- swarm votes (all llama3.1): Strategist 0.40 · Economist 0.40 · **Naturalist 0.80** · Skeptic 0.40

The reasoning is pure mean-reversion: it read "30% below average" as *low → room
to bounce*, the textbook stationary-noise assumption. A blockade is a **regime
break**, not noise — the low reading is the leading edge of a collapse, so the
model inverted the sign.

## Mechanism (two compounding faults)

1. **Naive reversion in the prompt.** `oracle.py` told the swarm chokepoint
   deviations are "physical evidence of trade disruption" but never forbade
   treating a *below-average* reading as a reversion opportunity. llama3.1
   drafted the rebound anyway.

2. **The oracle is blind to the war.** Live pull at audit time — the entire
   `conflict` domain held **one** event: `Ukraine warfront … (DeepStateMap)` @48.5,31.2,
   **3266 km** from Hormuz. No Gulf/Iran/Hormuz conflict signal anywhere in the
   structured feeds (ACLED was never wired up; coverage is essentially just
   Ukraine frontlines). The chokepoint anchor is also **~6-day lagged** (IMF
   PortWatch as of 2026-07-19: *"Strait of Hormuz: 15 daily ship transits, 30%
   below its 30-day average (21)"*), so even the "15/day" was stale.

Corroboration the oracle had but never connected: `[FUTURES]` in the same brief
showed WTI $86.34 (+1.68%) in steep **backwardation (−12.25%)**, Brent $93.60
(+2.85%), Gold $4154.9 (+2.06%) — a supply-shock signature — while VIX sat calm
at 16.8. The stress lived in `futures`; it never reached the Hormuz forecaster,
which looks only at transit counts.

## Fixes (engine, shipped with this audit — pending engine restart to deploy)

1. **`world_state.py` — conflict-proximity gate (new).** Pinned the fixed
   geography of the ~28 straits PortWatch tracks (`_STRAIT_COORDS`; transit rows
   carry no coords). `build_brief` now geocodes each chokepoint and, when a
   `conflict|unrest|geopolitical|displacement` signal of salience ≥ 0.6 sits
   within **700 km** *and reads as actual fighting/closure* (`_is_conflict`),
   tags that strait's brief line: `⚠ CONFLICT NEARBY (~67km): <nearest event>`.
   Bonus: chokepoints are now geocoded, so the UI can fly to a strait.

   **First live pass (12:53 EDT restart) exposed that category + salience alone
   is too loose** — the `geopolitical` feed is a grab-bag. Two false positives
   shipped for one brief and were tightened out the same session:
   - *Tsugaru Strait ← "Green flood alert in Japan"* — a flood alert tagged
     `geopolitical`, salience 0.8 via the `flood` keyword. A flood is not a
     blockade.
   - *Strait of Hormuz ← "Chokepoint HIGH: Strait of Hormuz"* (~15 km) — a
     self-referential maritime-risk annotation matching the strait to itself.
   - (*Kerch Strait ← "Ukraine warfront" (~537 km) was correct* — Kerch is
     genuinely in the war zone; kept.)

   Fix: `_is_conflict()` now requires **conflict vocabulary** (token-matched on
   word boundaries so `war` never fires on `warning`/`toward`; stems catch
   `militar-`, `blockad-`, `insurgen-`) and rejects any candidate whose title
   contains `chokepoint`. Salience floor 0.6 stays as a first cut.

2. **`oracle.py` — prompt hardening.** The `[CHOKEPOINTS]` guidance now states a
   below-average reading means flow is *already* reduced → forecast disruption
   **persisting or deepening, not a mechanical rebound** ("a strait under
   blockade, conflict or closure trends toward zero … only predict rising
   transits when the snapshot shows a concrete easing catalyst"), and a
   `⚠ CONFLICT NEARBY`-tagged strait "can shut outright — weight closure, not
   reversion."

3. **`osiris_intake.py` — the conflicts feed was being dropped at the door
   (found after deploy, root cause of the "starved gate").** The `/api/conflicts`
   route *does* carry the war — `zones` (`YEMEN WAR` @15.5,48 sev `war`, `RED SEA
   THREAT` @16,40, `GAZA CONFLICT`, `TAIWAN STRAIT`, …) and `liveEvents`
   (`Houthis … Entering U.S.-Iran War` @15.5,47.9), all geocoded. But it had **no
   dedicated parser**, so it fell to the generic `_find_items`→`_to_event` path,
   which (a) picks the *first* list-of-dicts (`zones`), (b) can't read a zone's
   `label` as a title (zones have no `title`/`name` key) → **`_to_event` returned
   `None` for all 15**, and (c) never reached `liveEvents`. Empirically: **0 of 15
   kept**; the only conflict signal reaching the brief was Ukraine, from the
   separate `/api/frontlines` parser. New `_conflicts_events()` parses both arrays
   (zone `label`→title, `severity`→salience `war`.95/`high`.8/`elevated`.65;
   liveEvent `title` + `_salience`), category `conflict`, coords preserved.
   `world_state._is_conflict` now trusts the curated `conflict` category outright
   (zone labels like "RED SEA THREAT" carry no vocab keyword yet ARE conflict);
   the vocabulary screen still guards the `geopolitical` grab-bag.

4. **`world_state.py` — pin `conflict` + name the most significant nearby zone
   (found post-deploy of #3).** Once #3 landed, the conflict domain jumped 1 → 31
   and the live brief exposed a second truncation bug (same class as the
   2026-07-18 anchor fix): `conflict`, un-pinned, ranked mid-pack and fell past
   the 6500-char cut — so the oracle's **prompt** saw the war only through
   chokepoint markers, never the warzones themselves (the proximity gate still
   worked, since it runs on the full event list *before* truncation). Fix:
   `conflict` added to the `pinned` anchor tuple. Also switched the marker from
   *nearest* conflict to *most significant* (highest salience, tie-broken by
   proximity) so a curated "UKRAINE WAR" zone is named over an incidental nearby
   headline. Verified: in a truncation-pressured brief, `[CONFLICT]` now renders
   in-prompt (top lines: "UKRAINE WAR", "Houthis … U.S.-Iran War", "YEMEN WAR").

The four are layered: (2) is the **feed-independent backstop** that kills the
inverted-rebound logic regardless of coverage; (1) **sharpens** it into a concrete
cue when a geocoded conflict signal is present; (3) **feeds it** — the war was in
the inputs all along, discarded at intake; (4) **surfaces it** into the oracle's
prompt so it can reason about the theatres, not just the chokepoints.

## Verification

- `py_compile` clean on both files.
- Real `build_brief` exercised against synthetic events (scratchpad
  `test_choke_gate.py`): (A) a nearby Bandar-Abbas strike (@27.2,56.3, 67 km)
  tags Hormuz and names the event; (B) the real 3266 km Ukraine event does **not**
  tag Hormuz; (C) a salience-0.4 routine geo event nearby is filtered by the 0.6
  floor; (D) the two live false positives — flood-alert-as-geopolitical and the
  self-referential "Chokepoint HIGH: Strait of Hormuz", plus a "storm warning"
  (proving `warning`≠`war`) — are all filtered; (E) genuine vocabulary
  (seized / missile / blockade / warfront) still trips the gate. Name-normalisation
  and haversine (Tehran 1118 km, Bandar Abbas 67 km) checked.
- **Live confirmation** after the 12:53 EDT restart: the brief's `[CHOKEPOINTS]`
  lines now carry coordinates (`@26.6,56.4` on Hormuz) — proof the new
  `build_brief` is running — and the Kerch↔Ukraine tag fired correctly. The
  vocabulary tightening (`_is_conflict`) needs a **second restart** to deploy.

## What the fixed feed actually shows (end-to-end, pre-deploy test vs live data)

Running `_conflicts_events` on the live `/api/conflicts` → 29 events (19 over the
0.6 floor), then through `build_brief` with the real chokepoints:

| chokepoint | marker |
|---|---|
| Bab el-Mandeb (0 ships!) | `⚠ CONFLICT NEARBY (~526km): RED SEA THREAT` |
| Suez Canal | `⚠ CONFLICT NEARBY (~217km): GAZA CONFLICT` |
| Taiwan Strait | `⚠ CONFLICT NEARBY (~56km): TAIWAN STRAIT` |
| Strait of Hormuz | *(clean)* |
| Malacca | *(clean)* |

**Honest note on Hormuz.** The trigger card was Hormuz, but the ingested conflict
data places the active fighting in the **Yemen / Red Sea / Gaza** corridor — and
it is **Bab el-Mandeb**, not Hormuz, that shows LIVE SHIPS: 0. Post-fix the system
correctly lights up that corridor and leaves Hormuz clean (transits reduced 30%
but no armed-conflict zone within 700 km in the data; nearest is ~1400 km in
Yemen). If there is Hormuz-specific fighting not yet in `/api/conflicts`, that is a
genuine upstream coverage question — but the "starved gate" itself is now fixed:
the war reaches the map.

## Remaining coverage note

`/api/news` (Hormuz ×7, Iran ×19) and `/api/unrest` (Bandar, Tehran) also carry
Gulf content but are not wired into the proximity gate — news headlines usually
lack coordinates, and `news` is intentionally outside `_CONFLICT_CATS`. Broader
structured conflict coverage (ACLED / Cloudflare Radar, unbuilt per CLAUDE.md)
remains the durable upgrade for geocoded per-event conflict; the `/api/conflicts`
zones now cover the major theatres in the interim.

## Systematic feed audit (the conflicts bug was a *class*, so we swept for the rest)

Method: drove every entry in `FEEDS` through the real `OsirisIntake._fetch_feed`
against live osiris and counted events produced vs. raw records present. The
signature of the bug = **route returns records, parser yields 0, failure swallowed**
(`_fetch_feed` only logs at `debug`). Of ~50 feeds, three more were silently blind:

| feed | route | records→events | verdict |
|---|---|---|---|
| **`risk`** (instability) | `/api/country-risk` | 20 → 0 | **real — same field-name bug** (items key on `code`, no `title`) |
| `crypto` (markets) | `/api/crypto` | 3 → 0 | real — shape drift (`_markets_events` expected a dict, route ships a list) |
| `radiation` (environment) | `/api/radiation` | 60 → 0 | **NOT a bug** — `_radiation_events` deliberately suppresses `NORMAL` stations; fires on a real spike |

Fixes (engine, ride the same restart):
1. **`_country_risk_events()`** — the significant one. Per-country instability index
   (Israel HIGH 100 `active_conflict`, Ukraine CRITICAL 99, Yemen/Palestine/Sudan
   CRITICAL, …) was contributing zero to a *world-forecasting* engine. New parser
   surfaces the elevated countries, geocoded via a curated `_RISK_COUNTRY`
   ISO2→(name,lat,lng) map; a country tagged `active_conflict` is emitted as a
   `conflict` event so it also feeds the chokepoint gate (verified: Suez tags
   `⚠ CONFLICT NEARBY (~312km): Country risk — Israel: HIGH (100/100)`),
   `post_conflict`/other → `geopolitical`. 12 events live.
2. **`_markets_events()`** — now also accepts the flat `[{symbol,price}]` list
   `/api/crypto` ships (confirmed live, not stale: BTC $65,990 ≈ live quote $66,028).

Not fixed (out of scope, logged): the `news`/`unrest` no-coords limitation above;
`flights` transient `ReadTimeout`; `reliefweb`/`metaculus` dormant (pending access).

## Adjacent-blindness fixes — make the two *silent* failure modes self-announce

Both bugs in this audit were invisible until someone eyeballed a card. Two changes
so the next one announces itself:

1. **`osiris_intake.py · _fetch_feed`** — a generic-path feed whose route returns
   records but yields **0 events** now logs at **`warning`** ("`N records but 0
   events parsed — shape/field mismatch`"), instead of nothing. Scoped to the
   generic path, so dedicated parsers that legitimately filter to 0
   (`radiation`-normal, dormant feeds) never trip it. This is the exact signature
   of the `/api/conflicts` + `/api/country-risk` bugs — now it can't hide.
2. **`world_state.py · build_brief`** — the 6500-char prompt cut is now
   **budget-aware and boundary-clean**: it renders whole sections until the budget
   is spent (pinned anchors always render), never cuts mid-section, and appends a
   `[+ N lower-signal sections trimmed for length: infrastructure(57), seismic(34)…]`
   note. The `[CONFLICT]`-fell-off-the-cliff failure is now impossible to miss —
   any elision is named in the prompt. Verified at live scale: 476 events →
   6810 chars (7200 backstop), all pinned sections present, 23 tail sections named.

## Divergence note

Engine files touched — all already on the CLAUDE.md "diverges from upstream" list:
`world_state.py`, `oracle.py`, `osiris_intake.py`. Mind these on any upstream pull.
No overlay copy-back (engine files, not `osiris/`).
