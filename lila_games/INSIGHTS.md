# INSIGHTS.md

Three things I discovered about LILA BLACK using the visualization tool.

---

## Insight 1: Human vs Bot combat is wildly imbalanced — but humans are winning

### What caught my eye
When I switched to Events mode and filtered for kill events, I noticed the sheer density of `BotKill` markers vs almost no `Kill` (human-vs-human) markers across all maps.

### The data
- **2,415** BotKill events (human kills bot)
- **700** BotKilled events (bot kills human)
- **3** Kill events (human kills human)
- **3** Killed events (human killed by human)

Human win rate vs bots: **77.5%**. Human-vs-human combat is almost non-existent — only 6 events across 796 matches and 5 days.

### What's actionable
This strongly suggests that:
1. **Player density per match is very low** — humans almost never meet each other, they only encounter bots
2. **Bots may be too weak** — a 77.5% human win rate means players rarely lose to AI, reducing tension
3. **The extraction shooter loop depends on conflict** — if players aren't fighting each other, the core threat is the storm and bots, not PvP

**Metrics affected:** Engagement rate, session length, match excitement rating
**Actionable items:**
- Increase human player count per match or reduce bot count to force human encounter
- Tune bot difficulty upward (or introduce "elite bot" tier) to raise the 22.5% win rate for bots
- Add spatial matchmaking to cluster human players into overlapping extraction zones

**Why a Level Designer should care:** If human paths never intersect, it means the map layout is either too large or extraction points are too spread out. The designer can add "chokepoints" — corridors, bridges, or mandatory loot rooms — that funnel players toward each other.

---

## Insight 2: AmbroseValley dominates, but Lockdown has disproportionate storm deaths

### What caught my eye
On the heatmap view, switching the Storm Deaths layer for Lockdown showed deaths concentrated near map edges — suggesting players are getting caught by the storm in a small map where they should have ample time to extract.

### The data
- **AmbroseValley:** 17 storm deaths across 61,013 position events (~0.028%)
- **Lockdown:** 17 storm deaths across 21,238 position events (~0.080%)
- **GrandRift:** 5 storm deaths across 6,853 position events (~0.073%)

Lockdown has **2.9x the storm death rate** of AmbroseValley despite being a smaller, supposedly faster-paced map.

### What's actionable
Players on Lockdown are dying to the storm at nearly 3× the rate of the main map. On a small, close-quarters map, this shouldn't happen — players should be able to reach extraction easily.

Possible explanations:
- Storm timing is too aggressive on Lockdown relative to map size
- Extraction points may be poorly placed relative to storm origin
- Players enter high-combat areas and get caught unaware

**Metrics affected:** Match completion rate, player frustration score, storm death % per map
**Actionable items:**
- Review storm shrink timing on Lockdown — consider adding 15-20 seconds to the extraction window
- Audit extraction point placement: are they inside or near the storm's final circle?
- Use the heatmap to identify where storm deaths cluster and check for obstacles blocking fast exit routes

**Why a Level Designer should care:** Storm deaths are a design failure on a small map. They signal that the storm's speed or direction doesn't match the layout — a fixable geometry problem.

---

## Insight 3: Loot density is heavily concentrated in AmbroseValley, suggesting GrandRift is underplayed and under-looted

### What caught my eye
Filtering by map and looking at Loot events, the density difference between maps was stark. Even accounting for match volume, GrandRift players loot far less per match.

### The data
| Map | Loot events | Matches | Avg loot per match |
|---|---|---|---|
| AmbroseValley | 9,955 | ~533 | **18.7** |
| Lockdown | 2,050 | ~183 | **11.2** |
| GrandRift | 880 | ~80 | **11.0** |

AmbroseValley players loot **70% more** per match than players on the other two maps. This isn't just because it's more popular — it's a per-match difference.

### What's actionable
Lower loot rates on GrandRift and Lockdown suggest either:
- Loot spawns are less dense or well-distributed on those maps
- Matches end faster (storm forces extraction before full looting)
- Players don't feel rewarded enough to explore those maps

**Metrics affected:** Session length per map, average items collected per match, map preference ratio
**Actionable items:**
- Review loot spawn density on GrandRift — add 2-3 high-value loot areas in under-trafficked zones to incentivize exploration
- Investigate whether GrandRift matches end prematurely (shorter time-to-extract) and balance accordingly
- Use the Paths view to check if there are entire zones of GrandRift that players never visit — these are candidates for loot redistribution or point-of-interest additions

**Why a Level Designer should care:** Loot is a primary motivator for exploration. Low loot uptake on GrandRift means players aren't engaging with large portions of the map — effectively wasting design work. Adding loot density where the traffic heatmap shows low activity will directly pull players into underused zones.
