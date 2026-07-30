# Crucible arena redesign

**Status:** DIRECTIONS IN REVIEW. Drafted 2026-07-30. Companion to
[crucible-simplification-plan.md](crucible-simplification-plan.md) §5d, which locked the
target but never designed it.

Sequencing per that plan: front door first (shipped 2026-07-29), then economy v2 (shipped
2026-07-29/30), then the arena. This is the arena.

---

## 1. Diagnosis, from the live screen

Captured 2026-07-30, signed in, game day 29, on the real production page.

**Desktop** is a three-column dashboard: a pools rail (season prize, treasure chest), a
boss portrait in a bordered box, a forge rail, then a Warden band, then a row of three
board panels, then a chest log, then a how-it-works list, then two footers. Seventeen
top-level components, about 1,500 lines of JSX.

**Mobile is the damning one: a 4,579px single-column scroll.** In order, a phone gets:
banner, real-money notice, quick-start card, boss art, season prize, treasure chest, the
forge, an auto-mine cross-sell, the Warden band, the leaderboard, the hunt log, my
earnings, the chest log, how it works, two footers.

Three specific failures fall out of that:

| # | Failure | Evidence |
|---|---|---|
| F1 | **The prize is not the hero.** | `$3.82` is the fourth number on the page, in a bordered card, at 26px. The front door leads with it at 150px. A player who came in through the door is *demoted* on arrival. |
| F2 | **The loop is not visible as a loop.** | Forge, swing, bank, and the chest are in four different containers, in three different columns, in an order that reads down-then-across. The single most common question ("what do I do?") is answered by a bulleted list at the bottom of a 4,579px scroll. |
| F3 | **The paid multiplier is buried.** | Offerings sit inside a collapsed band below the arena. Under economy v2 an offering is +15% on your share of the chest, which is the strongest paid action in the game, and it is behind a disclosure triangle. |

Everything else is a consequence of those three.

## 2. What the arena keeps (operator, 2026-07-30)

Five things, and nothing else:

1. The chest prize
2. The Warden
3. The weapon plus swing and bank
4. The offering prompt
5. A thin live ticker

Plus the day/season marker and a link out to Records.

## 3. What moves to Records

Not a dump of the leftover panels. It is the game's public record, and it doubles as the
trust surface the front-door red team asked for (self-verifying proof of life, on-chain).

| Section | Absorbs | Why it earns a place |
|---|---|---|
| **The chest** | chest log, plus a new headline: every crack so far, total paid, all with tx links | "14 wins, $96.20 paid, all on-chain" is the single most persuasive fact we own and it is currently a footnote |
| **Standings** | chest shares, winnings, season, as today's segmented control | Ranking is reference material. It is not something you act on mid-swing |
| **Your record** | my-earnings detail, hunts, spend, net, season points, prizes won with tx links | Personal accounting, read occasionally, never during a run |
| **The log** | full hunt feed, everyone and mine | The arena keeps three lines of it as a ticker; the full feed lives here |
| **Fairness** | commitment hash, seed reveal, how to verify | Currently a footer line under a 4,500px scroll, which is close to hiding it |

## 4. Open requirement the directions must absorb: where you buy crafts

`BuyCrafts` is one of the seventeen components and it is the only money-in step in the
game. It cannot go to Records (you would have to leave the arena to keep playing) and it
must not come back as a panel (that is the problem we are fixing).

**Resolution: buying is a drawer off the forge control, not a place.** When paid crafts
run out, the forge control becomes "Buy crafts", which opens a drawer over the arena. The
rORE mine-vs-buy cross-sell and the auto-mine card move into that drawer, where they are
in context, instead of standing as arena panels competing with the boss.

All three directions must be able to host that drawer. Whichever wins, this is part of the
build.

## 5. The three directions

Built as standalone HTML mockups, both player states (no weapon, mid-run), desktop and
mobile. Shared spec: [`docs/mockups/2026-07-arena/SPEC.md`](mockups/2026-07-arena/SPEC.md).

| | Thesis | Signature | Answers best |
|---|---|---|---|
| **A. The Purse** | The prize is the hero | Live counting pot with a fill rule; your stake as a stamped ticket; a fixed bottom action rail | "What do I make money on?" |
| **B. The Fight Bill** | The boss is the hero | Full-bleed Warden as the page, type overlaid like a boxing bill; the poster tears when the chest cracks | "Why is this worth my attention?" |
| **C. The Foundry Line** | The loop is the hero | Stations running left to right, live one lit; a molten channel pours banked damage into the chest | "What do I do, in what order?" |

## 6. How the winner gets picked

Scored, after an adversarial usability pass. Criteria and weights, set before the mockups
were seen so the scoring cannot be reverse-engineered to a favourite:

| Criterion | Weight | Test |
|---|---|---|
| Ten-second comprehension | 30 | Can a stranger state the prize, the order of actions, and what pays them, without scrolling? |
| Action reachability | 15 | Swing control above the fold on desktop and in the thumb third on mobile, in both states |
| Offering visibility | 15 | Does the paid multiplier get noticed without a disclosure being opened? |
| Distinctiveness | 15 | Would this be recognisable as Crucible with the wordmark removed? |
| Motion payoff | 10 | Does landing a swing feel like hitting something? |
| Multi-community durability | 10 | Works unchanged on an Axie day, an Ember day, and a future partner's day |
| Build cost against the live React page | 5 | How much of the existing component tree survives |

The adversarial pass is the same "RONKE don't read" red team used on the front door: ten
hostile personas with a ten-second budget, each reporting the first moment they would
bounce, then refuters against the findings most likely to be imaginary. Results land in
§7.

## 7. Red team

Ran 2026-07-30. Ten hostile personas at a ten-second budget across all three directions,
then three adversarial refuters against the findings most likely to be imaginary and most
expensive to act on wrongly.

### 7.1 What the personas ranked

First-place votes: **C five, B three, A two.** Last-place votes: **A four, C four, B two.**
Deeply split, and the split is not noise: each direction won on a different axis and each
lost badly on another.

| Persona | 1st | Why, in their words |
|---|---|---|
| Reads nothing | B | the gorilla with the gold chest is the only thing that looked like treasure |
| One thumb, train | A | the only screen where the thumb never moves |
| No wallet | B | the only one that showed a boss, a pot and a button before asking for anything |
| Ronkeverse holder | B | the only one that reads as an event for us rather than a dashboard we tinted |
| Scam hunter | C | the only one that shows the price and the odds in the same box as the button |
| EV calculator | C | the only place damage per dollar is computable |
| Lapsed player | C | the rail draws a line with no nightly pool on it |
| Axie holder, off-day | C | treats today's Warden as a costume on a machine, not as a gate |
| Low vision, outdoors | C | state carried by brightness rather than hue |
| Ceiling hunter | A | the only one carrying evidence the pot moves |

### 7.2 Findings that survived

**Confirmed, act on these.**

1. **`Banked` is two different quantities on one screen.** The weapon readout says
   `banked 18` (this run) and the roster says `926 banked` (since the last crack), about
   90px apart on mobile. Present in **all three directions**, inherited from the shared
   spec, and **live in production today** (`index.jsx:1232` against `index.jsx:1116`).
   Fix is one word: the run pile stays `Banked`, the cycle figure becomes **`Your stake`**.
2. **"+15% harder" never has a counterpart.** Every screen states an advantage held by
   somebody else and never states the baseline, so an Axie player on a Ronke day reads a
   gate rather than a bonus. Nine words, three layouts, same failure. Needs the other half
   of the sentence: everyone swings, holders swing harder.
3. **Nothing says the Warden rotates.** No screen tells a visiting community that its own
   day is coming. The Axie persona's soft bounce was not the 15% line, it was the two
   seconds after it spent looking for a return date and finding none. One line
   ("Beast Warden tomorrow") converts a permanent exclusion into an appointment.
4. **The holder perk and the paid offering are both +15%, worded identically.** Hold the
   NFT and hit 15% harder; or pay 6.9 $RONKESTR and hit 15% harder, three times a day. To
   a Ronkeverse holder that reads as: holding gets me what a stranger buys, in my own
   token. This is an economy question, not a layout one, and it is on the operator's desk.
5. **The offering is priced in tokens while the pot is priced in dollars.** `152 $RONKESTR
   ($0.72)` sits inches above `Feed the Warden 6.9 $RONKESTR` with no dollar figure. Two
   personas independently read that asymmetry as deliberate.
6. **An upsell must not precede the game.** C puts the feed slot above the forge, as the
   first interactive element on the page. Four personas bounced there, and one that does
   not hold the token read `0 / 3 today` as a slot they were locked out of.
7. **The craft price disappears in the armed state.** All three show `about $0.03` only on
   the forge screen. The armed screen is where repeat spending happens and it carries no
   price at all.
8. **Accessibility, all cheap:** banked versus at-risk are distinguished only by hue at
   about 10px, which is money state carried by colour alone; the micro-type status ribbons
   are unreadable outdoors; and C's station rail is inverted, with the step you are on
   dimmer than the steps you have not reached.
9. **After a crack, the three players who did not find it watch their stake drop to zero
   with no explanation.** The crack cinematic renders for the finder only. Scale-independent,
   hits active players every cycle, and nobody had noticed. Found by a refuter, not a persona.

**Refuted, do not act.**

- **"Demote the pot; a four-dollar prize set 200px tall reads as a joke."** Four personas,
  and it dies. The number ranges from $1.00 at a reseed to a modelled $27 to $73 at the
  growth this redesign exists to cause, so it cannot decide a permanent hierarchy; the
  personas judged the hero at a trough. More fundamentally they misread the object: under
  economy v2 the pot is the **denominator of the player's own score**, not an advertisement.
  Demote it and `$0.62` and `20.4%` become percentages of nothing. The bounce framing is
  also invalid on a screen no persona in that set ever reaches, since the arena sits behind
  the door and behind sign-in.
  **What survived:** the pot must read as a **meter, not a billboard**. Drop the count-up
  animation the spec mandated (a giant animated money figure is on our own list of things
  that trigger scam-detection, written a week ago), replace `last crack, day 27 · $2.11`
  with the cumulative record, and screenshot the winner at the $1.00 reseed floor before
  shipping, because days 1, 2 and 3 all cracked at the floor back to back.
- **"Put proof on the arena: contract address, tx links, payout history, fairness."**
  Refuted. Every item is already assigned to Records or already on the door, which now
  fires for every visitor once per game day and carries a last-chest-paid line. The 64%
  rake figure runs on a false premise the persona supplied itself: the chest is treasury
  funded and grows whether anyone plays or not. **What survived:** two things, neither on
  the arena. Give the `Records` link a payload rather than leaving it a bare noun, and add
  a "where the money comes from" line to the Records spec, because as drafted this redesign
  deletes the only answer to that question from the entire signed-in experience.
- **"The arena must announce that the rules changed."** Refuted. The screens state the new
  rules three times in present tense, and one of those statements sits between the two
  elements alleged to mislead. The cohort is not hypothetical and it is not 4-6 people: of
  76 handles, 21 ever banked a hunt, and exactly **two** have been idle more than 21 days,
  one of which is an operator test handle. The other played fifteen hunts and earned eleven
  cents. Present-tense negation serves returners, newcomers and strangers identically and
  never expires; a change notice serves one person once and then becomes permanent debris
  on a screen being cut to five things.
- Also re-refuted, having failed once already on the front door: the live player count, and
  "cheapness makes the prize feel fake."

## 8. Scores and recommendation

Scored against §6, weights fixed before the mockups existed.

| Criterion | Wt | A Purse | B Fight Bill | C Foundry Line |
|---|---:|---:|---:|---:|
| Ten-second comprehension | 30 | 16 | 20 | **26** |
| Action reachability | 15 | **15** | 11 | 7 |
| Offering visibility | 15 | **13** | 10 | 9 |
| Distinctiveness | 15 | 11 | **15** | 6 |
| Motion payoff | 10 | 8 | **9** | 6 |
| Multi-community durability | 10 | 7 | 3 | **9** |
| Build cost | 5 | **4** | 2 | 3 |
| **Total** | **100** | **74** | **70** | **66** |

Four points separate first from second and eight cover the field. That is not a winner, it
is three partial answers, and the per-criterion column tells you why: **A owns the hands,
B owns the eyes, C owns the head.** Each one's best element was named by name, repeatedly,
by personas who ranked that direction last overall.

### Recommendation: build D, a synthesis, on A's spine

Same call the front door reached, for the same reason.

- **A's spine.** The pot as the hero and as a meter, your stake welded underneath it as a
  stamped ticket, and the action rail pinned to the bottom of the viewport in both states.
  The pinned rail is the single best mechanic in the set and it is the reason A wins the
  criterion that the whole mobile funnel runs through.
- **C's station rail, as a thin strip rather than four boxes.** `Forge · Strike · Bank ·
  Chest`, current step brightest, carried at the top of the action rail. It is the most
  praised object in the entire exercise, named by five personas including three who ranked
  C first because of it and one who called it the only thing that taught them the game
  without jargon. What it must not bring with it is C's four bordered columns, which are
  the dashboard we are escaping.
- **B's boss scale and its roster block.** The Warden large enough to be a presence, and
  the four-row banked/share list, which the low-vision persona called the most readable
  object anywhere in the set and the EV persona called the only place the denominator was
  honestly published.
- **Not from B: the community takeover.** B repaints the page in the partner's colours and
  the visiting Axie player hard-bounced in under two seconds, correctly reading it as
  somebody else's marquee. The Warden is a costume on the machine, which is C's instinct
  and the durable one across nine Axie classes, Ember days, Ronke days and every partner
  after them.

Everything in §7.2 that survived is part of that build, not a follow-up.

### D as built (2026-07-30)

`docs/mockups/2026-07-arena/arena-d-synthesis.html`, both states, both breakpoints.

What it takes from where:

| From | What | Why |
|---|---|---|
| A | pot as hero, stake stamped under it, action rail pinned to the bottom | wins reachability, the criterion the mobile funnel runs through |
| C | `Forge · Strike · Bank · Chest` as a line **inside** that rail | most-praised object in the exercise, without C's four bordered columns |
| B | Warden at full scale, and the banked/share roster | boss presence, and the only honest publication of the denominator |
| nobody | the partner-colour page repaint | the visiting Axie player bounced on it in under two seconds |

Findings applied, all from §7.2:

- Pot is a **meter, not a billboard**: the count-up is gone, the fill line stays, and
  `last crack $2.11` is replaced by `7 cracks so far · $23.17 paid out · biggest $7.89`.
- `Banked` split in two: the run pile in the rail keeps **Banked**, the cycle figure
  everywhere else becomes **Your stake** (stamp, roster column, ticker, odds line).
- The perk gets its counterpart: *"Everyone swings and everyone can crack it. Ronkeverse
  holders hit 15% harder."*
- A rotation line: *"Tomorrow Beast Warden · Axie beast, bug and mech"*, with the sprite.
- The offering is priced in dollars like everything else: `6.9 $RONKESTR, about $0.03`.
- The craft price **survives into the armed state**, on the station line.
- The offering sits beside the controls, never above the game the way C's feed slot did.
- Banked versus at-risk no longer differ by hue alone: solid underline against dashed.
- The station line's live step is the **brightest** thing on it, not the dimmest.
- `Records` carries a payload: *"Records · every chest paid"*.

Two composition problems found and fixed while building, both by looking at screenshots
rather than at the markup: the roster was laid over the Warden's face, which threw away
the one thing B was contributing; and the sprite's own near-black plate read as a visible
rectangle once the boss was bright enough to be a presence, fixed with contrast plus a
radial mask rather than by dimming the boss back down.

**Still open, deliberately not in D:** the +15%/+15% collision between the holder perk and
the paid offering (§7.2 item 4) is an economy question, and the post-crack explanation for
the players who did not find it (item 9) belongs to the crack surface, not the arena.

