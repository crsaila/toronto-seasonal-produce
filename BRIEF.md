# Toronto seasonal produce calendar — project brief

## What this is

An interactive Gantt-style chart showing when ~70 fruits and vegetables are in season in the Toronto / southern Ontario area, broken down by week across all 52 weeks of the year. It includes field-grown, greenhouse, storage, and foraged items, with regional highlights called out.

## Key features

- **52-column week grid** per produce item, colour-coded by season type (peak, shoulder, greenhouse, storage)
- **"Now" marker** — a red vertical line showing the current week
- **Week hover** — moving the cursor across the chart highlights the column and shows a tooltip with the month/week label, how many items are in peak or shoulder season, and the top few by name
- **Row hover** — the row under the cursor gets a subtle background tint and the label text bolds
- **Filters** — search by name, filter by type (fruit/vegetable), source (field, greenhouse, foraged, storage), and sort (A–Z, earliest peak, by type)
- **Regional highlights toggle** — 13 items uniquely tied to the Toronto/southern Ontario foodshed (wild leeks, fiddleheads, garlic scapes, morels, chanterelles, haskap berries, pawpaw, sour cherries, currants, gooseberries, elderberries, Saskatoon berries, wild grapes) are marked with a coral left border and can be filtered to show only those
- **Stats bar** — showing count, items currently in season, and number of regional highlights
- **Footnote** with data sourcing and caveats

## Data sources

- **Foodland Ontario availability guide** (Government of Ontario): https://www.ontario.ca/foodland/page/availability-guide — the primary source, gives month-level availability per item including greenhouse
- **Ontario Greenhouse Vegetable Growers**: https://www.ogvg.com/ — confirms year-round greenhouse production of tomatoes, cucumbers, peppers, lettuce, strawberries, eggplant
- **Farmers' Markets Ontario**: https://www.farmersmarketsontario.com/whats-in-season-at-ontario-farmers-markets/
- **Sobeys Ontario produce guide**: https://www.sobeys.com/en/articles/whats-season-guide-canadian-produce-ontario/
- **Pick Your Own harvest calendar** (southern Ontario focus): https://pickyourown.org/CNONharvestcalendar.htm
- Foraged items (ramps, fiddleheads, morels, chanterelles) informed by blogTO, Ontario Culinary, Toronto Region Conservation Authority reports, and Forbes Wild Foods references

Week-level granularity is synthesised from month-level data — southern Ontario (Niagara, Essex County) typically runs 1–2 weeks ahead of the GTA. The past decade of warming has generally extended seasons by 1–3 weeks.

## Important implementation notes

### Use HTML, not React, if publishing as a Claude artifact

The published artifact environment does not reliably render React components the same way the in-chat preview does. The HTML version works identically in both contexts. If you only need it within a Claude chat, React is fine.

### Safari / cross-browser fixes that matter

- `flex: "1 1 0%"` not `"1 1 0"` — Safari requires the unit on zero flex-basis
- Use `marginRight` / `marginBottom` instead of `gap` on flex containers
- Use `1px` borders, not `0.5px` — Safari rounds sub-pixel borders inconsistently
- Add `-webkit-` prefixed properties: `WebkitAppearance`, `WebkitOverflowScrolling`, `WebkitTransform`
- Hardcode colour values rather than relying on CSS custom properties (the published artifact sandbox may not inject them)
- Use regular `function` expressions rather than arrow functions for broadest compatibility
- For row hover: derive the hovered row index from the parent container's `mousemove` Y coordinate using fixed row heights (26px height + 2px margin = 28px), rather than attaching `onMouseEnter`/`onMouseLeave` to individual rows (Safari in sandboxed iframes can swallow these)
- Replace emoji with CSS-styled letter badges ("F" / "V") — emoji render inconsistently across WebKit versions
- Use `appearance: menulist` (with `-webkit-` prefix) on `<select>` elements

## Colour scheme

| Season type | Colour | Hover colour |
|---|---|---|
| Peak | `#1D9E75` | `#178a66` |
| Shoulder | `#9FE1CB` | `#88d4bb` |
| Greenhouse | `#378ADD` | `#2d78c5` |
| Storage | `#D3D1C7` | `#c4c2b8` |
| Regional accent | `#D85A30` | — |
| Now marker | `#E24B4A` | — |
| Fruit badge | bg `#FAECE7`, text `#993C1D` | — |
| Vegetable badge | bg `#E1F5EE`, text `#0F6E56` | — |

## Data structure

Each item follows this shape:

```
{
  name: "Wild leeks (ramps)",
  type: "V",           // "F" for fruit, "V" for vegetable
  regional: true,      // true = uniquely tied to this region
  seasons: [
    {
      start: 15,       // week number (1-indexed, week 1 = first week of Jan)
      end: 21,
      intensity: "peak",   // "peak" | "shoulder" | "greenhouse" | "storage"
      note: "Foraged — harvest sustainably"
    }
  ]
}
```

An item can have multiple season entries (e.g. apples have storage, shoulder, peak, and late shoulder spans across different weeks).

## Month-to-week mapping

| Month | Start week |
|---|---|
| Jan | 1 |
| Feb | 5 |
| Mar | 9 |
| Apr | 14 |
| May | 18 |
| Jun | 23 |
| Jul | 27 |
| Aug | 31 |
| Sep | 36 |
| Oct | 40 |
| Nov | 44 |
| Dec | 49 |

## Full produce list (71 items)

### Fruits (24)
Apples, Apricots, Blueberries, Cherries (sweet), **Sour cherries** ★, Cranberries, **Currants (red/black)** ★, **Elderberries** ★, **Gooseberries** ★, Grapes, **Haskap berries** ★, Muskmelon / cantaloupe, Nectarines, Peaches, Pears, Plums, Raspberries, Rhubarb, **Saskatoon berries** ★, Strawberries (field), Strawberries (greenhouse), Watermelon, **Pawpaw** ★, **Wild grapes** ★

### Vegetables (47)
Asparagus, Beans (green/yellow), Beets, Bok choy, Broccoli, Brussels sprouts, Cabbage, Carrots, Cauliflower, Celery, Corn (sweet), Cucumbers (field), Cucumbers (greenhouse), Eggplant, **Fiddleheads** ★, Garlic, **Garlic scapes** ★, Kale, Leeks, Lettuce (field), Lettuce (greenhouse), **Morel mushrooms** ★, Mushrooms (cultivated), Napa cabbage, Onions (cooking), Onions (green), Parsnips, Peas (green/snow), Peppers (field), Peppers (greenhouse), Potatoes, Pumpkin, Radishes, **Wild leeks (ramps)** ★, Rapini, Rutabaga, Spinach, Squash (summer/zucchini), Squash (winter), Sweet potatoes, Tomatoes (field), Tomatoes (greenhouse), Daikon radish, Sprouts, Artichoke, Bitter melon, **Chanterelle mushrooms** ★

★ = regional highlight (13 total)

## Prompt to recreate

If starting fresh in a new Claude chat, something like this should get you there:

> Build me a self-contained HTML file (no frameworks) for an interactive Toronto seasonal produce calendar. It should be a Gantt-style chart with 52 weekly columns, a row per produce item (~70 items including fruits, vegetables, greenhouse, foraged, and storage crops), colour-coded by season type (peak, shoulder, greenhouse, storage). Include: a "now" marker for the current week, week hover with a tooltip, row hover highlighting, filters (search, type, source, sort), a toggle for regional highlights (items unique to southern Ontario like wild leeks, fiddleheads, garlic scapes, morels, chanterelles, haskap, pawpaw, sour cherries, currants, gooseberries, elderberries, Saskatoon berries, wild grapes), and stat cards. Use Foodland Ontario data. Must work in Safari — no CSS variables, no 0.5px borders, use flex: 1 1 0% not 1 1 0, no emoji, derive row hover from parent mousemove Y position not per-row event handlers.
