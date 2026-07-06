# El Paso Housing: New Growth vs. Old Core, 2010–2025

**Live dashboard:** https://hoffmanap.github.io/housinggrowth

A parcel-level analysis comparing new single-family construction in El Paso, Texas's four highest-growth suburban zip codes against a second dataset covering the city's five historic central zip codes — looking at how much got built, how big, how dense, how it's priced, and how the new suburbs actually differ from the old core.

---

## How it was made

**Growth-area data:** Parcel-level property records for four high-growth El Paso zips (79911, 79934, 79936, 79938), from [RentCast.io](https://www.rentcast.io). 17,113 single-family homes with a recorded year built in one of two windows, 2010–2014 or 2020–2025 (2015–2019 is not present in the source data).

**Central-core data:** Parcel records for five historic central zips (79901, 79902, 79903, 79905, 79930), from [Regrid.com](https://www.regrid.com). 817 parcels total, spanning a continuous build/renovation year from 2010–2024 with no gap. 670 (82%) are single-family (Regrid usecode `A1`); the rest are small multifamily structures — duplex through fourplex (`B1`–`B4`). All cross-region comparisons use single-family parcels only, to stay apples-to-apples with the growth-area data.

**Processing (Python / pandas), for both sources:**
- Parsed nested/JSON-like fields — `taxAssessments` (growth) for the latest county-assessed value; `features` (growth) for garage, fireplace, cooling, room count, and garage spaces.
- Normalized inconsistent boolean fields (`ownerOccupied` had mixed `True`/`False`/`"1.0"`/`"0.0"` string representations).
- Computed a floor-area ratio (finished/building sqft ÷ lot sqft) as a density proxy for both sources.
- Sale price was evaluated and excluded from both datasets — it's recorded for under 2% of growth-area parcels and only 7 of 670 central-core single-family parcels (the rest are `$0` placeholders for "no qualifying sale on file"). County-assessed value is used instead throughout.
- Aggregated everything by period/zip/year for the growth areas, and by zip/year for the central core, plus point-level tables (lat/long, sqft, year, zip) for both, for mapping.

**Front end:** Unchanged approach — a single self-contained HTML file, no build step, no server. Charts render with [Chart.js](https://www.chartjs.org/) (now loaded from jsDelivr — the previous cdnjs URL pointed to a version cdnjs never actually published, which is why charts silently failed to render before). The hero map is a hand-built canvas scatter of every growth-area parcel by real latitude/longitude. All data is embedded directly in the page as JSON, so it works fully offline.

**Design:** Same desert-survey/parcel-ledger aesthetic as before — rust (2010–2014) vs. turquoise (2020–2025) for growth-area period comparisons, plus a new gold-vs-plum pairing for growth-suburbs-vs-central-core macro comparisons, so the two kinds of comparison never visually blur together.

---

## Key findings

### 1. Growth-area construction fell 32% overall — but 79938 never actually collapsed
With the corrected data, total new single-family homes across the four growth zips fell from 10,172 (2010–2014) to 6,941 (2020–2025) — a 31.8% decline, not the 78–93% drop suggested by the earlier, incomplete extract.

| Zip | Area | 2010–2014 | 2020–2025 | Change |
|---|---|---|---|---|
| 79938 | Tierra del Este / far east | 6,998 | 4,562 | ‑34.8% |
| 79934 | Northeast (Campo del Sol/Sandstone Ranch / Mesquite Hills) | 1,685 | 1,192 | ‑29.3% |
| 79911 | Cimarron/Upper Valley | 1,050 | 1,041 | ‑0.9% |
| 79936 | East-Central / Pebble Hills | 439 | 146 | ‑66.7% |

79938 alone still accounts for roughly two-thirds of all new construction in both periods (68.8% → 65.7%). The far east didn't get displaced by the northwest — it just cooled off somewhat, like the rest of the market.

### 2. A data-quality lesson worth keeping
The previous version of this analysis, built on an incomplete extract, showed 79938 with **zero recorded homes from 2020–2024** and all activity landing in 2025 alone — which read as either a construction pause or a dating artifact. The completed dataset shows steady, year-by-year construction throughout the 2020s instead. The "gap" was missing data, not a real pattern. Worth remembering any time a single zip's trend looks like a discontinuity: check for missing records before concluding something changed in the real world.

### 3. Homes are bigger, denser, with fewer fireplaces
- Average square footage: 1,807 → 2,023 sqft (+12%); average lot size barely moved (6,974 → 6,965 sqft) — so density (floor-area ratio) rose from 0.301 to 0.323.
- Average bedrooms: 3.50 → 3.71; but average total room count fell (8.28 → 7.46) — newer homes pack more bedrooms into fewer overall rooms.
- Garage (reported in ~85% of homes) and central cooling (~83%) are effectively universal in both periods. Fireplaces, by contrast, are a real amenity casualty of newer construction — though the field is sparsely reported (~19% of 2010–2014 homes vs. ~4% of 2020–2025 homes have it recorded at all), so treat the direction as the finding, not the exact magnitude.
- Average assessed value: $266,672 → $330,902 (+24%); value per sqft: $151.59 → $159.31 (+5%).
- Owner-occupancy rose slightly, from 91.1% to 93.8%.

### 4. Old core vs. new growth: closer on size than you'd expect, further apart on density and value
Comparing the four growth zips (17,113 homes) against the five central-core zips (670 single-family homes):

| Metric | Growth Suburbs | Central Core | Difference |
|---|---|---|---|
| Avg. square footage | 1,895 | 1,870 | +1.3% |
| Median square footage | 1,798 | 1,607 | +11.9% |
| Avg. lot size | 6,970 sqft | 8,320 sqft | ‑16.2% |
| Density (FAR) | 0.310 | 0.263 | +17.9% |
| Avg. assessed value | $288,523 | $252,064 | +14.5% |
| Value per sqft | $154.21 | $134.51 | +14.6% |
| Single-family share | 100% | 82% | — |

- **Average home size is nearly a wash** between the two geographies — the "new homes are bigger" assumption doesn't hold up against the old core as a whole. But the central core's median is well below its mean, pointing to a skewed mix of modest older homes plus a smaller number of large historic ones.
- **Central-core lots are actually bigger on average**, driven largely by spacious lots in 79902 — so despite similar house sizes, the growth suburbs are meaningfully denser.
- **Growth suburbs carry higher assessed value and price per square foot.**
- **The central core has a real multifamily presence (18% duplex-to-fourplex) that the growth suburbs, by definition of the source data, don't have at all (0%).**
- **79902 (Kern Place / Sunset Heights-adjacent) is the outlier inside the core** — averaging 2,694 sqft and $458,987 in assessed value, above every other central zip and above most growth-suburb zips too. The rest of the core (79901, 79903, 79905, 79930) is considerably more modest, averaging under 1,900 sqft and under $230,000.

---

## Files

- `el_paso_housing_analysis.html` — the full interactive dashboard (deployed to the GitHub Pages link above)
- `el_paso_housing_data.json` — the standalone processed/aggregated dataset (growth + central + macro comparison), if you want the numbers without the front end

## Caveats worth repeating

- Growth-area data covers only 2010–2014 and 2020–2025; 2015–2019 is not present. Central-core data is continuous 2010–2024.
- "Value" = latest county tax assessment on file for both sources, not a sale price or market estimate.
- Amenity fields (fireplace especially) and central-core bedroom counts (60% coverage) are sparse — read amenity and bedroom findings as directional.
- Central-core square footage comes from recorded building area, not a standardized "finished square footage" field, so size comparisons across sources are approximate.
- Sample sizes get small at the zip level for the central core (13–184 homes per zip) — treat single-zip central-core figures as illustrative, not statistically robust.
