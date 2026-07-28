# Brain audit — causal admissibility gates for surfaced couplings

2026-07-27. Trigger: the daily Opus brain review (`infra/brain-review.py`, advisory
and read-only by design) flagged the **same two defects six mornings running**
(07-22, 07-23, 07-24, 07-25, 07-26, 07-27). Statistics alone had stopped being
enough to decide what the brain says in public.

## Symptom

Of 12 surfaced connections on the live board, the top-scored ones had no
transmission mechanism:

| score | connection | what's wrong |
|---|---|---|
| 20.76 | china exposure → NG=F **down** | NG=F is Henry Hub — US-domestic, weather/storage driven. China demand is a marginal, lagged LNG factor, not a 1-day driver |
| 9.31 | equity volatility → NG=F down | no mechanism |
| 7.26 | agriculture & grains → NG=F down | no mechanism |
| 7.13 | defense → BTC-USD down | no transmission from defense news to bitcoin at 1d |
| 4.55 | shipping & oil chokepoints → NG=F down | wrong basin **and** wrong sign — chokepoint stress tightens seaborne energy, it should push UP |
| 2.90 | risk-off → GC=F **down** | causally backwards; risk-off classically bids gold up |

Two patterns underneath, both named by the reviewer:

1. **Symbol overload / noise sink.** NG=F absorbed 6 of 11 surfaced couplings
   (CL=F ~12 of 20 emerging). A fat-tailed contract makes *any* theme look
   coupled, and `placebo_p` under-controls precisely there — the drift it tests
   against is just as fat-tailed, so china→NG=F scored p=0.012 while being
   nonsense. *"Only causal review will catch these; the stats won't."*
2. **Sign inversion.** Where the mechanism is written down and the learned cell
   points the other way, the cell is far more often a sign error than a discovery.

## Fix — `engine/brain.py`, three gates, all presentation-only

The existing `_surfaces()` (one shared gate for `/brain` connections, `/watch`
annotations, `picks`, and the oracle's LEARNED MARKET MEMORY prompt block) now
composes the placebo test with a new `_mechanism_ok()`. `market_calls()` consults
`_mechanism_ok()` too, so a card can never ship on a coupling the connections list
refuses to show — the same principle as the 2026-07-19 ranking-alignment fix,
extended from *"is it drift?"* to *"is there a mechanism?"*.

| gate | rule | constant |
|---|---|---|
| **Sink quarantine** | a symbol clearing the placebo bar under **more than 3 distinct themes** is a sink; its *non-native* pairings stop surfacing. Native pairings (`sym in THEME_SYMBOLS[theme]`) are never quarantined | `SINK_THEME_MAX = 3` |
| **Discovery bar** | any other non-native pairing (the watchlist-discovery path — nobody wrote a mechanism for it) must clear **half** the normal placebo budget | `DISCOVERY_PLACEBO_MAX = 0.10` |
| **Sign conflict** | a cell contradicting a hand-written `DIRECTION_PRIORS` sign must clear a far stricter bar, and never speaks below n=5. No baseline to test against ⇒ no inversion claim | `SIGN_CONFLICT_PLACEBO_MAX = 0.05`, `SIGN_CONFLICT_MIN_N = 5` |

Design notes:

- **3 is not a tuned number** — it is the widest breadth the hand-written map gives
  any symbol (CL=F and GC=F sit at 3), so the bar can never bite a symbol operating
  inside its own written mechanism.
- **Nothing is deleted.** `assoc`, episode identities, folding and call grading are
  untouched; the gates decide only what is *said*. A quarantined cell keeps
  learning and can surface later on stronger evidence.
- `_sink_syms()` is deliberately non-recursive (it consults `_placebo_p`, never
  `_surfaces`) and cached per epoch beside `_shadow_map`; both caches drop on the
  same mutation points.
- Sinks are auditable at `/brain → status.sink_symbols`.

## Effect on the live board (measured on a copy of `runs/brain_state.json`)

Sink detection flags exactly **`NG=F`** today. Surfaced connections **12 → 3**:

```
KEPT     20.51  agriculture & grains  → ZC=F      up    n=5  p=0.046
         16.85  rates & safe havens   → EURUSD=X  down  n=7  p=0.012
         16.66  equity volatility     → EURUSD=X  down  n=5  p=0.051

DROPPED  20.76  china exposure        → NG=F      down  p=0.012  [sink/non-native]
          9.31  equity volatility     → NG=F      down  p=0.067  [sink/non-native]
          7.26  agriculture & grains  → NG=F      down  p=0.112  [sink/non-native]
          7.13  defense               → BTC-USD   down  p=0.184  [discovery bar]
          6.08  china exposure        → FXI       up    p=0.056  [sign-inverted]
          4.55  shipping & chokepoints→ NG=F      down  p=0.013  [sink/non-native]
          4.54  grid & utilities      → NG=F      down  p=0.140  [sink/non-native]
          3.02  risk-off              → NG=F      down  p=0.140  [sink/non-native]
          2.90  risk-off              → GC=F      down  p=0.175  [sign-inverted]
```

The three survivors are exactly the three the reviewer called credible. Every
dropped row is on its spurious list, with one exception worth stating plainly:

- **china exposure → FXI up** was called *credible* ("FXI **is** China large-cap;
  definitional") — but the written prior for that pairing is **down** (a China-risk
  flare knocks Chinese equities), and the learned cell says up at n=4, p=0.056. It
  fails the sign gate on both counts. The reviewer endorsed the *coupling*, not the
  direction; the cell can surface once it clears n≥5 at p≤0.05. Deliberate.

Honest consequence: for energy topics the oracle's LEARNED MARKET MEMORY prompt
block is now **empty** — every energy-theme coupling it had was junk. That is the
true state of the evidence, and it matches the reviewer's blunt verdict that the
learned layer has shown no standalone skill yet (4 hit / 4 miss vs a 0.48
drift null).

## Not done — deliberately left to Harvey

Review action #4, "stop letting the standalone learned basis size positions"
(gate learned-only calls behind prior confirmation), changes the CALL confidence
model. The graded record behind it is 4-4 for learned-alone and 1-4 for
forecast-vs-learned — far too small to re-weight on, and the CALL card is still
staging-gated, so nothing on air depends on it. Flagged, not changed.

## Tests

`Pythia/tests/test_brain_gates.py` — 12 tests (stdlib unittest):
`uv run python tests/test_brain_gates.py`. Covers sink detection and its
native-pair exemption, the n≥min_n floor, epoch cache invalidation, the discovery
bar vs the native bar, sign conflict with/without a baseline and below min-n, and
composition with the placebo test (a placebo failure still wins). The existing
17-test calibration suite still passes.

## Deploy

Engine-only change; takes effect on the next `systemctl restart pythia-engine`
(broadcast-safe — the engine unit does not own the X server or the encoder).
No overlay copy-back: `brain.py` is already on the CLAUDE.md "diverges from
upstream" list.
