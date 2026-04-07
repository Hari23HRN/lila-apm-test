# ARCHITECTURE.md

## What I Built

A browser-based player journey visualization tool for LILA BLACK, designed for Level Designers who need to understand spatial player behavior across maps.

---

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Data pipeline | Python + PyArrow + Pandas | Fast parquet reading; familiar ecosystem; great for batch ETL |
| Frontend | Vanilla HTML/JS + Canvas API | Zero dependencies; fast rendering; works offline; no build step |
| Hosting | Static file serving (Vercel/Netlify) | All data is pre-processed JSON — no backend needed |
| Data format | JSON (pre-processed) | Simple, universally readable; enables instant client-side filtering |

**What I explicitly avoided:** React (overkill for a single-page tool), WebGL (complexity not needed at this scale), a backend server (all queries are fast enough client-side with 796 matches).

---

## Data Flow

```
Raw .nakama-0 files (parquet, no extension)
        │
        ▼
  Python pipeline (process_data.py)
   ├─ Read all ~1,250 parquet files with PyArrow
   ├─ Decode event column from bytes → UTF-8 string
   ├─ Classify is_bot by checking if user_id is numeric
   ├─ Convert world (x, z) → minimap pixel coords per map
   ├─ Sample Position events (every 3rd) to reduce payload size
   └─ Aggregate heatmap grids (64×64) per map per event type
        │
        ▼
  matches.json   (~2.8 MB)   — all match paths + events
  heatmaps.json  (~110 KB)   — pre-aggregated heatmap grids
  match_index.json            — lightweight match list for sidebar
        │
        ▼
  index.html (static, loads JSON at runtime)
   ├─ Sidebar: filter + match list
   ├─ mapCanvas: draws minimap image
   ├─ overlayCanvas: draws paths, events, heatmaps
   └─ Timeline: scrubs match replay by ts offset
```

---

## Coordinate Mapping Approach

The README provides exact map configuration values. For each map:

```
u = (x - origin_x) / scale
v = (z - origin_z) / scale
pixel_x = u × 1024
pixel_y = (1 − v) × 1024   ← Y flipped: game origin = bottom-left, image origin = top-left
```

Key decisions:
- Used **x and z** for 2D plotting (y = elevation, not useful for top-down)
- Y-axis flip is applied at **pre-processing time**, not render time — simpler frontend
- Coordinates are clamped to [0, 1024] to handle rare edge cases where players leave map bounds
- All coordinate conversion happens once in Python; the frontend only receives pixel coordinates

---

## Human vs Bot Detection

Simple filename-based logic from README:
- **UUID user_id** (e.g. `f4e072fa-...`) → human player
- **Numeric user_id** (e.g. `1440`) → bot

Applied at load time; stored as `is_bot: boolean` in JSON. Visualized as green (human) vs red (bot) paths.

---

## Assumptions Made

| Situation | Assumption |
|---|---|
| `y` column | Ignored for 2D plotting (elevation only) |
| Timestamp interpretation | Treated as match-relative time offset, not wall clock |
| February_14 partial data | Included without special treatment; labeled as partial in README |
| Path sampling | Every 3rd position event sampled to keep JSON under 3MB |
| Heatmap resolution | 64×64 grid chosen as balance between detail and render speed |
| Match_id uniqueness | One match_id = one unique game session |

---

## Major Tradeoffs

| Decision | Option A (chosen) | Option B (considered) | Rationale |
|---|---|---|---|
| Data delivery | Pre-process to JSON | Stream parquet from server | No backend needed; fast first load |
| Rendering | Canvas 2D | WebGL/Deck.gl | Canvas is sufficient at this scale; much simpler |
| Path data | All paths in memory | Lazy-load per match | 796 matches × small paths fits in ~3MB |
| Heatmap | Server pre-aggregated | Client-side aggregation | Pre-aggregation is instant; no lag on layer switch |
| Framework | Vanilla JS | React | A data viz tool doesn't need component lifecycle management |
