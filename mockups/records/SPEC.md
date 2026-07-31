# Crucible Records — UI refresh

Mockup for operator sign-off. **Nothing is live.**

- **v2 (current)** — [`records-v2.html`](records-v2.html) · [desktop](shots/v2-desktop.png) · [mobile](shots/v2-mobile.png)
- v1 (superseded) — [`records-refresh.html`](records-refresh.html) · [desktop](shots/after-desktop.png)
- Before — [desktop](shots/before-desktop.png) · [mobile](shots/before-mobile.png)

v2 follows two external adversarial reviews and an internal 4-proposal / 3-lens
judge panel. Every figure is real, read from the live API, `ember.db`, and the
27 payout files on 2026-07-31.

---

## What v1 got wrong

**Three of my own numbers were wrong, and one fed the reviewers' sharpest attack.**

| v1 claimed | verified truth | how v1 got it wrong |
|---|---|---|
| 4 chests cracked in 30 days (~1 per 7.5 days) | **7 cracks in 29 nights (~1 per 4.1 days, 24% of nights)** | read `/api/chest-log`, which returns only the last 20 draws |
| all-time paid `$148.71` | components `$113.67 + $35.05` = **`$148.72`** | rounded the total instead of the components |
| — | the `payouts` table is **missing day 1** ($1.00) and sums to $34.05 | the 27 payout **files** sum to $35.045004 and all carry a tx hash |
| JohnnyCola `$47.09`, Puck `$9.94` | **$51.67**, **$16.89** | showed chest-only earnings as lifetime totals |

The crack-cadence error mattered most: one reviewer built their strongest attack on
"one chest every 7.5 days — not 'pays out daily'." The real cadence is nearly twice
as good. **Recompute every figure at build time from one source in one pass**, and add
a test asserting the ledger footer equals the sum of the displayed rows.

## What the reviews got wrong

Verified before acting on, and **not** implemented as written:

- **"Bump `--rule` to alpha `.22`"** — that yields **1.83:1**, still far below the 3:1
  it was meant to reach. Real value is ~`.40`. v2 uses a tiered scale instead.
- **"The segmented controls are fake / decorative"** — an artifact of reading a static
  mockup. They are fully wired in the live app (`onClick={() => setBoard(...)}`). The
  *ARIA-state* half of that finding was valid and is implemented.
- **"The operator funded one launch prize pool"** — the $113.67 is 50% of Crucible's
  **Ronin Proof-of-Distribution** reward: external money from Ronin's builder
  programme. That is a stronger credibility fact, not a weaker one, and v2 says so.
- The panel's own prescribed hairline `#3a332c` is **1.56:1**, not the "~3:1" it claimed.

## What both reviews got right (all implemented)

1. **Don't hero `$148.71`.** It let a skeptic compute $4.96/day and $7.08/player
   instantly, in 74px type. The project's own earlier red team killed a live-player
   count for exactly this reason.
2. **Restore an inline explainer.** v1 deleted the only two places a newcomer could
   learn the vocabulary, while *increasing* jargon density.
3. **Faint hairlines** (1.40:1 / 1.18:1) were carrying all the page structure.
4. **Nested mobile feed scrolling** was a touch trap that bought only 73px.
5. **Semantic headings, control state, focus rings** were missing.
6. **Cross-sell before verification** read as an acquisition funnel.

## The v2 design

**Spine: evidence-first** (panel winner, 19/30 — took the skeptic and craft lenses).
Every claim sits next to the transaction that proves it, and the unflattering
arithmetic is printed by us, in the smallest type on the page, at the foot of our own
ledger.

**The one rule that carries the design:**

> **Solid + a hash = money that left the wallet. Outline + no hash = money that is owed.**

Pre-verbal, costs no extra colour, extends free to any future liability. Best idea the
panel produced; kept verbatim.

**Deliberate departure from the panel.** Its winning proposal made a raw 66-character
hash the largest object on the page. The newcomer lens scored that **2/10** — "the
largest object is 66 characters of noise" — and the brief asks for something a human
can digest. So the readable **amount** leads and the hash sits directly beneath it as
a prominent, clickable credential. Evidence adjacency is kept; legibility is not
sacrificed to make a point.

```
provenance strip — 47 transactions · 30 days · last payment 2 days ago
┌────────────────────────────────────────────┬──────────────────────┐
│ THE LAST TIME THIS GAME PAID ANYBODY       │ OWED RIGHT NOW       │
│ $1.00 USDC → 5 wallets                     │  chest    $5.38 ▢    │
│ 5 named recipients, summing to $1.000000   │  season 3 ~$52  ▢    │
│ the transaction, clickable                 │  the colour law      │
├────────────────────────────────────────────┴──────────────────────┤
│ WHAT THE WORDS MEAN — 6 terms, plain English                      │
├───────────────────────────────────────────────────────────────────┤
│ EVERY PAYMENT, NEWEST FIRST — date/event/amount/wallets/source/tx │
│ …reconciliation footer, smallest type: the averages, the split    │
├─────────────────┬─────────────────────┬───────────────────────────┤
│ Everyone paid   │ The chest, night by │ Live activity             │
│ (21 wallets)    │ night + cadence     │                           │
├─────────────────┴─────────────────────┴───────────────────────────┤
│ provably fair · forge sink · full rules  →  THEN the rORE strip   │
└───────────────────────────────────────────────────────────────────┘
```

**Anonymous state solved structurally.** There is no "Your ledger" to empty out. The
module is *"Everyone who has been paid — 21 wallets"*, sorted by **most recent payment,
not by amount** (with the sort key printed, so nobody can claim we floated a winner to
the top). Connecting a wallet only pins your own rows. Same component, both audiences.

**The 76% season split is owned as a column, not a paragraph** — every ledger row
carries `GAME POT` or `RONIN PROGRAMME`, so there is no split left for a skeptic to
"reveal."

**The chest log stops reading as failure.** Rollovers are reframed as what they are —
the pot growing — with the cadence stated up front (7 cracks in 29 nights) and a
29-bar spark where orange = cracked.

## Accessibility — measured, not asserted

Ratios against `#100d0b`:

| token | value | ratio | use |
|---|---|---|---|
| `--bone` | `#f4ede1` | 16.64 | headings, paid amounts |
| `--dim` | `#b3a695` | 8.12 | body prose |
| `--muted` | `#9a8d7d` | 5.98 | labels, timestamps |
| `--ember` | `#ff5a12` | 6.20 | the one accent |
| `--line-ctl` | `#6a6158` | **3.19** | control borders — passes WCAG 1.4.11 |
| `--line` | `#524940` | 2.20 | section boundaries |
| `--line-soft` | `#3a332c` | 1.56 | dense row dividers (decorative, exempt) |

Timestamps no longer use `opacity:.7` (that was **3.50:1** on 11px — a fail).
Real `h1`/`h2`/`h3`, `aria-pressed` on segmented controls, visible focus rings, and
**no nested scrolling** at any breakpoint. On mobile the 6-column ledger becomes a
3-line record grid (one-field-per-line cost ~1,400px of extra scroll).

## Requires a backend change

The chest payout tx hashes **exist** — `paid.txHashes` in all 27 files under
`game/server/payouts/` — but **no API exposes them**. The exhibit and the ledger both
depend on this. Needs one read-only endpoint:

```
GET /api/payments → [{ day, dateISO, paidAtISO, totalSent,
                       txHashes[], wallets[{wallet, usdc, handle}],
                       result: {type:"win"|"rollover", winner, amount, ronkestr},
                       inKind: {symbol, tokens, txHash} | null }]
```

Read the **files**, not the `payouts` table (the table is missing day 1). Cache ~5 min.
**Read-only** — `payouts/` is gitignored and is the recovery source of truth; a test
already destroyed a real paid record there on 2026-07-29.

`/api/past-payouts` already returns the 19 per-winner season hashes. No other backend
work is needed.

## Open calls for the operator

1. **The reconstructed-record footnote.** The 2026-07-29 payout file carries a note
   that it was rebuilt after an internal test overwrote it. v2 discloses this in small
   type under the exhibit, because a page arguing for transparency cannot suppress the
   one caveat attached to its own hero. It is defensible either way — the transactions
   were confirmed on-chain before the overwrite, so the money is not in question, only
   our bookkeeping. **Say the word and I'll cut it.**
2. **Publishing the full recipient roll** (21 wallets + amounts). These are already
   visible on-chain inside the batched transactions we link to, so this adds
   convenience rather than exposure — but it is a choice.
3. **Also in this pass:** un-gate `/records` from the front door, and fix the three
   surviving `"Push"` strings (renamed `"Attack"` in the arena on 2026-07-30).

## Residual risk

**The recency hero goes stale, and the staler it gets the worse it looks.** Cracks
average one per four days but the observed gaps include two 8-day stretches. On a bad
week the exhibit will show an eight-day-old $1.00 payment — real, correctly labelled,
and screenshot-able as "the game stopped." v2 blunts this (cadence line, "longest gap
so far: 8 days" beside the live chest, the day-stepper) but does not remove it.
Selecting by *largest* payment instead would fix the optics and destroy the argument,
so the fixed, printed selection rule stays.

## Not changed

No money logic, no settle/payout path, no change to the fairness scheme. Same
components and data sources; the view is still switched by `useLocation()`.
