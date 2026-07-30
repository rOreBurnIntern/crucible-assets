# Crucible arena redesign — shared build spec

**Read this whole file before writing a line of code.** Three directions are being built
against it in parallel. Everything below is identical across all three; only the layout
thesis differs, and that arrives in your own brief.

---

## 0. Design read

Reading this as: **the action screen of a browser game** for NFT-community players
(Ronkeverse, Axie, Burncoin) who arrive from a tweet or a Discord link, in the
**poster / print language already established by this product's front door**, leaning
toward native CSS, condensed display type, hard-edged blocks, and motion that reads as
physical impact rather than decoration.

Dials: **DESIGN_VARIANCE 8 · MOTION_INTENSITY 7 · VISUAL_DENSITY 3.**

This is a redesign in *overhaul* mode for the screen itself, but *preserve* mode for the
brand: the front door's visual language is fixed material you inherit, not something to
reinvent. The screen it replaces is a 17-panel dashboard; the whole point of this
exercise is that it stops being a dashboard.

## 1. What this screen is for

A player lands here and needs three answers inside ten seconds:

1. **What is the prize?** ($3.82 in the chest, right now, real USDC)
2. **What do I do, in what order?** (forge a weapon, swing at the Warden, bank the damage)
3. **What do I earn?** (banked damage IS your share of the chest; crack it and take 20% more)

If a stranger cannot answer all three without scrolling, the direction has failed
regardless of how it looks.

## 2. The only things allowed on this screen

Everything else moved to a separate Records page. Do not add anything to this list.

1. **The chest prize.** The headline number.
2. **The Warden.** Today's boss, in its community's colour.
3. **Your weapon and the swing/bank controls.** The loop.
4. **The offering prompt.** A paid damage multiplier that must be impossible to miss.
5. **A thin live ticker.** Three lines maximum of what other players just did.

Plus: a small persistent link out to Records, and the day/season marker.

Explicitly NOT on this screen: hunt log, chest log, winnings board, season standings,
earnings breakdown, mine-vs-buy card, the "how it works" list, the fairness footer, the
"forged in so far" totals.

## 3. Both states are required

Build **both** and put a small dev-only toggle in the corner (label it `state`) so a
reviewer can flip between them. It is not part of the design; keep it visually quiet.

**State 1 — no weapon.** The player has paid crafts but has not forged. The forge action
is the primary control. Weapon odds are visible: Common 60% · Rare 25% · Epic 12% ·
Legendary 3%.

**State 2 — weapon in hand, mid-run.** A Rare sword (1d6), swing 7 of the run, past the
5-swing safe floor. Banked 18, at risk 24, next-push shatter risk 30%. Primary control is
now Swing/Push; Bank is the secondary control. This is the state that has to feel like a
game.

## 4. Real data (use these exact values, they are live as of game day 29)

| Thing | Value |
|---|---|
| Game day | 29 |
| Chest total | **$3.82** |
| Chest composition | $3.10 USDC plus 152 $RONKESTR worth $0.72 |
| Today's Warden | **Ronke Warden** (Ronkeverse holders hit 15% harder) |
| Crack odds | about **1 in 3,000** swings |
| Finder's cut | **20%** to whoever cracks it, the rest splits by banked damage |
| The player | handle `skmd`, 926 damage banked, 20.4% of the chest |
| Player's stake | **$0.62** if it cracks now, **$1.39** if they crack it themselves |
| Others banked | Puck 2,042 (45.1%) · JohnnyCola 1,545 (34.1%) · xInes007 16 (0.4%) |
| Season | S3, 13d 16h left, prize ~$52 (~1,093 RON) |
| Craft cost | about $0.03 each, paid in $BURN + rORE + $RONKESTR |
| Offering | send 6.9 $RONKESTR to the treasury for +15% damage, up to 3 per day |
| Last crack | JohnnyCola, day 27, $2.11 including 414 $RONKESTR |
| Weapon tiers | Common 1d4 · Rare 1d6 · Epic 2d6 · Legendary 3d6 |
| Safe floor | first 5 swings cannot shatter |

Ticker lines (use verbatim, they are real):

```
Puck banked 38 · 2m ago
JohnnyCola pushed a Rare and shattered · 3m ago
xInes007 forged a Common · 6m ago
```

## 5. Copy

Write like the front door: short, declarative, second person, sentence case except in
display type. Every string below is approved. You may cut, you may not embellish.

- `$3.82` / `in the chest`
- `incl. 152 $RONKESTR ($0.72)`
- `Your share $0.62` / `$1.39 if you crack it`
- `Any swing can crack it open. About 1 in 3,000.`
- `Banked damage is your share.`
- Forge control: `Forge a weapon`
- Swing control: `Swing` (safe) / `Push · 30%` (risky)
- Bank control: `Bank 24`
- Offering: `Feed the Warden 6.9 $RONKESTR. Every swing hits 15% harder. Up to 3 today.`
- Records link: `Records`

**Zero em-dashes and zero en-dashes anywhere in visible text.** Regular hyphen only.
This is a hard fail condition, not a preference.

## 6. Tokens

Inherited from the front door. Do not invent a new palette.

```
--ink:    #100d0b   /* warm near-black page */
--bone:   #f4ede1   /* type, rules, stamps */
--muted:  #9a8d7d   /* labels, secondary */
--ember:  #ff5a12   /* money AND your action. Nothing else. */
--warden: #27B9FC   /* today's community. Ronkeverse blue. */
```

Two accents with two jobs, locked: **ember means money or your move**, **warden means
today's host**. If you need a third colour, you have made a structural mistake.
Risk/shatter states use ember at reduced alpha or bone, never a new red.

**Type** (already loaded in the app, use only these):

- Display: `'Big Shoulders Display'`, weight 800, uppercase, tight leading (0.84), slight
  negative tracking. This is the poster voice. Use it big and use it rarely.
- Body: `'Sora'`, 400/600/700.
- Numerals: `'JetBrains Mono'` for anything a player counts (damage, risk %, tokens).
  Money headline numbers may be display face instead if that reads better.

Load with: `<link href="https://fonts.googleapis.com/css2?family=Big+Shoulders+Display:wght@600;800&family=Sora:wght@400;600;700&family=JetBrains+Mono:wght@400;600;700&display=swap" rel="stylesheet">`

**Shape lock: radius 0 everywhere.** Hard edges, hairline rules, flat fills, offset block
shadows (`box-shadow: 6px 6px 0 var(--bone)`) rather than blurred ones. No glassmorphism,
no soft cards, no glow.

**Sprites:** `assets/wardens/ronke.png` (today's boss), `assets/weapon-rare.png`,
`assets/weapon-common.png`, `assets/chest-closed.png`, `assets/chest-open.png`,
`assets/coin.png`. All are pixel art: always set `image-rendering: pixelated` and scale by
whole-ish factors. Never blur them, never round them, never put them in a rounded card.

## 7. Motion (MOTION_INTENSITY 7, and every piece must be justifiable)

Required, because they carry meaning:

- **Swing feedback.** Clicking swing must produce a physical hit: the boss flinches, a
  damage numeral punches out and rises, the screen shakes briefly. This is the single most
  important interaction on the page; spend your motion budget here.
- **Risk rising.** As shatter risk climbs the risk indicator should visibly tighten or heat
  up. The player should feel the danger without reading the number.
- **The pot is alive.** The chest figure counts up on load rather than appearing static.

Optional if it serves your direction: an ambient Warden idle, a crack cinematic.

Banned: infinite loops on things that are not live, decorative particles everywhere,
parallax for its own sake. `prefers-reduced-motion: reduce` must collapse everything to
static; this is mandatory.

## 8. Layout requirements

- **Desktop first, 1440x900.** The three answers in section 1 fit above the fold with no
  scroll. Design this first.
- **Mobile 390x844 must also work** and is a real deliverable, not an afterthought. The
  swing control belongs in the bottom third of the screen where a thumb reaches it. State
  the collapse explicitly in your CSS.
- The page sits inside an existing left sidebar on desktop (about 165px). Assume your
  design starts to the right of it. Do not draw the sidebar.
- No card grid. No three equal columns. No centered-everything.

## 9. Anti-slop conditions (any one of these fails the build)

- An em-dash or en-dash in visible text.
- More than the two accent colours.
- A rounded corner.
- A blurred drop shadow used as elevation.
- Numbers a player counts set in a proportional face.
- A "01 / 02 / 03" numbered-step decoration. (If your direction genuinely IS a sequence,
  name the steps by their verbs: Forge, Swing, Bank. Never number them.)
- Emoji anywhere in the design. The existing screen is full of them; that is part of what
  we are fixing.
- A section eyebrow above every block.
- Fake data that contradicts section 4.
- The swing control below the fold on either breakpoint.

## 10. Deliverable

One standalone HTML file, self-contained (inline CSS and JS, no build step, no
frameworks), referencing sprites at `assets/...`. It must open correctly from `file://`
and from a static server.

Save to the exact filename given in your brief. Then screenshot your own work at both
breakpoints, look at the screenshots, and fix what is weak before reporting. Use a session
name unique to you so parallel builds do not collide:

```
playwright-cli -s=<yourname> open "file:///home/openclaw/projects/burncoin/docs/mockups/2026-07-arena/<yourfile>.html"
playwright-cli -s=<yourname> resize 1440 900
playwright-cli -s=<yourname> screenshot --filename /tmp/<yourfile>-desktop.png
playwright-cli -s=<yourname> resize 390 844
playwright-cli -s=<yourname> screenshot --filename /tmp/<yourfile>-mobile.png
playwright-cli -s=<yourname> close
```

Iterate at least twice against your own screenshots. A first draft you have not looked at
is not a deliverable.

Report back: the file path, your one-line design read, the signature element in one
sentence, and anything in this spec you deliberately broke and why.
