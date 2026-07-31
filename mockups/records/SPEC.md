# Crucible Records — UI refresh

Mockup for operator sign-off. Nothing is live. Built 2026-07-31.

- Mockup: [`records-refresh.html`](records-refresh.html)
- Before: [`shots/before-desktop.png`](shots/before-desktop.png) · [`shots/before-mobile.png`](shots/before-mobile.png)
- After: [`shots/after-desktop.png`](shots/after-desktop.png) · [`shots/after-mobile.png`](shots/after-mobile.png)

Every figure in the mockup is real, read from the live API and `ember.db` on
2026-07-31 (day 30). The personal column is skmd's actual row.

---

## Why

`/records` was created on 2026-07-30 when the arena moved to Direction D — the
arena kept five things and everything else was moved here. It was a **move, not
a design**: the page still wears the old "Obsidian Foundry" chrome (16px-radius
frosted panels, backdrop-blur, the molten-crack top edge, mixed Cinzel/Sora/mono
headings) while the arena it links back to is flat ink, zero-radius, hairline
rules and Big Shoulders numerals. It reads as a different product.

That was the known item on the deferred polish list. Auditing it turned up three
more, one of which is not cosmetic.

## What's wrong today

**1. ~40% of the desktop page is void.** `.cru-boards` is `repeat(3, 1fr)` with
`align-items: start`. Column 3 is a stack of three panels (earnings, chest log,
how-it-works); columns 1 and 2 hold one short panel each. Column 3 runs roughly
3× taller, so the bottom-left half of the page is empty. Full-page height 2,127px.

**2. The page's best number is not on it.** Records exists to be the proof page.
But all-time payouts live in two places that are never added together: chest/USDC
earnings on the Winnings board (**$35.04**) and season RON prizes in a per-player
row (**$113.67** paid in S2 alone). The real figure is **$148.71 to 21 players**,
and a visitor had no way to see it. This is the strongest, most trust-building
sentence the game can say.

**3. Duplicated rules copy, and it is stale.** The "How it works" panel and the
footer paragraph both restate mechanics that `about.html` owns — which the project
decided against on 2026-07-07 (single source of truth: about.html is canonical for
game rules). Both also still say **"Push"**, renamed **"Attack"** in the arena on
2026-07-30. So the two places that disagree with the live game are both on this page.

**4. Smaller things.** The chest panel prints `$5.38` and the $RONKESTR split twice
inside one card. On mobile the rail's "Connect Wallet" pill overlaps the handle chip.

## What the refresh does

Two full-width bands over three self-balancing list columns:

```
┌────────────────────────────────────────────────────────┐
│ THE RECORD    paid all-time │ in the chest │ season 3   │
├────────────────────────────────────────────────────────┤
│ YOUR LEDGER   earned │ riding │ on the line │ season    │
├──────────────────┬─────────────────┬───────────────────┤
│ Boards           │ Chest log       │ Hunt log          │
│ (chest/winnings/ │                 │ (everyone/mine)   │
│  season)         │                 │                   │
└──────────────────┴─────────────────┴───────────────────┘
  rORE auto-mine strip
  provably-fair hash · forge sink · one link to full rules
```

The three columns are all list-shaped, so they stay roughly level on their own —
no rigid grid holding a void open. The ledger goes horizontal instead of being a
640px tower, which is what unbalanced the old grid in the first place.

Result: **1,401px desktop** (was 2,127px), **3,232px mobile** (was 3,305px), and
nothing below the fold is empty.

Visual language is Direction D, unchanged from the arena: `--ink #100d0b`,
`--bone #f4ede1`, `--muted #9a8d7d`, `--ember #ff5a12`, Big Shoulders Display for
numerals, JetBrains Mono for kickers and data, Sora for prose, zero border-radius,
hairline rules instead of card borders.

**The chest composition bar keeps its colours** — that is the one sanctioned
exception to the two-accent lock, agreed in the arena D work: there the colour is
the data (ember = USDC, Ronkeverse blue = $RONKESTR).

## Also in this pass

- **Un-gate `/records` from the front door.** Today `if (doorDue) return <door>`
  fires before the `onRecords` branch, and the door is due once per day for
  *everyone*, so the arena's own "Records · every chest paid" link bounces you to
  the door. Records is the read-only proof page — the one page a skeptic should
  reach without a wall. The door still shows on the arena.
- **"Push" → "Attack"** in the two Records strings and the one arena hint toast
  (`index.jsx:823`, `1701`, `1715`).

## Open question for the operator

**Is `$148.71` a number we want as the page's biggest type?** It is honest, it is
~4× what the page implies today, and the red team's own finding was *show the
evidence, not the claim*. But it is still a small number, and the same red team
refuted the live-player-count idea because a thin figure "reads as opportunity to
a sharp and as abandonment to everyone else."

Alternatives if it should not lead: make **"in the chest right now"** the lead and
demote all-time paid to the sub-line, or drop the dollar figure and lead with
**"4 chests cracked · 21 players paid · 30 days"**.

I have built it leading, because Records is the proof page and burying the proof
defeats the point — but this is a positioning call, not a design one.

## Not changed

No money logic, no API wiring, no settle/payout path. Same components, same data
sources, same `CruciblePage` component with the view switched by `useLocation()`.
