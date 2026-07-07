# El Paso Housing: New Growth vs. Old Core, 2010–2025

**Live dashboard:** https://hoffmanap.github.io/housinggrowth

A parcel-level analysis comparing new single-family construction across El Paso, Texas's four highest-growth suburban tracts against a second dataset covering the city's five historic central zip codes — looking at how much got built, how big, how dense, how it's priced, and how the new suburbs actually differ from the old core.

---

## How it was made

**Growth-area data:** Parcel-level property records for four high-growth El Paso tracts, from [RentCast.io](https://www.rentcast.io):

- 79911 — Northwest: Upper Valley / Cimarron
- 79934 — Northeast: Campo Del Sol / Sandstone Ranch / Mesquite Hills
- 79936 — East-Central: Pebble Hills / Vista Real
- 79938 — Far East: Tierra del Este

17,113 single-family homes, built in one of two five-year windows this analysis is scoped to: 2010–2014 and 2020–2025.

**Central-core data:** Parcel records for five historic central zips (79901, 79902, 79903, 79905, 79930), from [Regrid.com](https://www.regrid.com). 817 parcels total, spanning a continuous build/renovation year from 2010–2024. 670 (82%) are single-family (Regrid state use code `A1`); the remaining 147 (18%) are small multifamily structures — duplex through fourplex (`B1`–`B4`).

**Processing (Python / pandas), for both sources:**
- Parsed nested/JSON-like fields — `taxAssessments` (growth) for the latest county-assessed value; `features` (growth) for garage, fireplace, cooling, room count, and garage spaces.
- Normalized inconsistent boolean fields (`ownerOccupied` had mixed `True`/`False`/`"1.0"`/`"0.0"` string representations).
- Computed a floor-area ratio (finished/building sqft ÷ lot sqft) as a density proxy for both sources.
- Built three parallel aggregates for the central core — single-family only, multifamily only, and both combined — by zip and by year, feeding a live toggle on the dashboard.
- Sale price was evaluated and excluded from both datasets — it's recorded for under 2% of growth-area parcels and only 7 of 670 central-core single-family parcels (the rest are `$0` placeholders for "no qualifying sale on file"). County-assessed value is used instead throughout.
- A small number of growth-area parcels (81, under 0.5% of the file) carry coordinates well outside El Paso County — clear geocoding errors. These are excluded from the map visualization only; they remain part of every count and calculation elsewhere in the report.

**Front end:** A single self-contained HTML file — no build step, no server, no external script dependency. Chart.js is embedded directly in the file rather than loaded from a CDN. The hero map is a hand-built canvas scatter of every growth-area parcel by real latitude/longitude, with period and zip filters and hover tooltips. All data is embedded as JSON in the page, so the whole thing works offline once downloaded (only the Google Fonts stylesheet is external, and the page falls back to system fonts gracefully if that's unreachable).

**Design:** A desert-survey/parcel-ledger aesthetic — rust (2010–2014) vs. turquoise (2020–2025) for growth-area period comparisons, gold vs. plum for growth-suburbs-vs-central-core macro comparisons.

---

## Key findings

### 1. Growth-area construction fell 32% overall — but the Far East is still the engine
Total new single-family homes across the four growth tracts fell from 10,172 (2010–2014) to 6,941 (2020–2025) — a 31.8% decline.

| Tract | Name | 2010–2014 | 2020–2025 | Change |
|---|---|---|---|---|
| 79938 | Far East: Tierra del Este | 6,998 | 4,562 | ‑34.8% |
| 79934 | Northeast: Campo Del Sol / Sandstone Ranch / Mesquite Hills | 1,685 | 1,192 | ‑29.3% |
| 79911 | Northwest: Upper Valley / Cimarron | 1,050 | 1,041 | ‑0.9% |
| 79936 | East-Central: Pebble Hills / Vista Real | 439 | 146 | ‑66.7% |

79938 alone still accounts for roughly two-thirds of all new construction in both periods (68.8% → 65.7%), building steadily across every year of the 2020s window rather than in a single burst.

### 2. Homes are bigger, denser, with fewer fireplaces
- Average square footage: 1,807 → 2,023 sqft (+12%); average lot size barely moved (6,974 → 6,965 sqft) — so density (floor-area ratio) rose from 0.301 to 0.323.
- Average bedrooms: 3.50 → 3.71; but average total room count fell (8.28 → 7.46) — newer homes pack more bedrooms into fewer overall rooms.
- Garage (reported in ~85% of homes) and central cooling (~83%) are effectively universal in both periods. Fireplaces have all but disappeared from newer construction, though the field is sparsely reported, so treat the direction as the finding, not the exact magnitude.
- Average assessed value: $266,672 → $330,902 (+24%); value per sqft: $151.59 → $159.31 (+5%).
- Owner-occupancy rose slightly, from 91.1% to 93.8%.

### 3. Old core vs. new growth: closer on size than you'd expect, further apart on density and value
Comparing the four growth tracts (17,113 homes) against the five central-core zips, single-family only (670 homes):

| Metric | Growth Suburbs | Central Core (SF only) | Difference |
|---|---|---|---|
| Avg. square footage | 1,895 | 1,870 | +1.3% |
| Median square footage | 1,798 | 1,607 | +11.9% |
| Avg. lot size | 6,970 sqft | 8,320 sqft | ‑16.2% |
| Density (FAR) | 0.310 | 0.263 | +17.9% |
| Avg. assessed value | $288,523 | $252,064 | +14.5% |
| Value per sqft | $154.21 | $134.51 | +14.6% |

- **Average home size is nearly a wash** between the two geographies. The central core's median sits well below its mean, though, pointing to a skewed mix of modest older homes plus a smaller number of large historic ones.
- **Central-core lots are actually bigger on average**, driven largely by spacious lots in 79902 — so despite similar house sizes, the growth suburbs are meaningfully denser.
- **Growth suburbs carry higher assessed value and price per square foot** — at least when the central core is measured single-family only. Switch the toggle to "Multifamily Only" and this flips: central-core multifamily parcels average $398,166 and $185.60/sqft, both above the growth-suburb single-family figures.
- **79902 (Kern Place / Sunset Heights-adjacent) is the outlier inside the core** — averaging 2,694 sqft and $458,987 in assessed value, above every other central zip and above most growth-suburb tracts too. The rest of the core (79901, 79903, 79905, 79930) is considerably more modest.

---

## Files

- `el_paso_housing_analysis.html` — the full interactive dashboard (deployed to the GitHub Pages link above)
- `el_paso_housing_data.json` — the standalone processed/aggregated dataset (growth + central, with all three central-core filter views), if you want the numbers without the front end

## Caveats worth keeping in mind

- Growth-area analysis is scoped to two five-year windows (2010–2014, 2020–2025), by design. Central-core data is a continuous 2010–2024 span.
- "Value" = latest county tax assessment on file for both sources, not a sale price or market estimate.
- Amenity fields (fireplace especially) and central-core bedroom counts (60% coverage) are sparse — read amenity and bedroom findings as directional.
- Central-core square footage comes from recorded building area, not a standardized "finished square footage" field, so size comparisons across sources are approximate. For multifamily parcels in particular, this may reflect total building area across units rather than a single unit's living space.
- Sample sizes get small at the zip level for the central core, especially once split by property type — the toggle's zip-level table and chart will show this directly as you switch views.
