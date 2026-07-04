# El Paso New-Construction Housing, 2010–2025

**Live dashboard:** https://hoffmanap.github.io/housinggrowth

A parcel-level analysis of 10,342 single-family homes built in three high-growth El Paso, Texas zip codes, comparing two five-year windows — 2010–2014 and 2020–2025 — to see how much, how big, how dense, and where new housing got built.

---

## How it was made

**Data source:** Property records for El Paso, TX from [RentCast.io](https://www.rentcast.io), covering three zip codes — 79911 (Canutillo / Vinton, far northwest), 79936 (East-Central / Pebble Hills / Vista Real), and 79938 (Tierra del Este / Horizon City, far east). The raw file contains 10,342 single-family homes with a recorded year built in one of two windows: 2010–2014 or 2020–2025. (2015–2019 is not present in the source data — this is treated as a genuine gap throughout, not a decline to zero.)

**Processing (Python / pandas):**
- Parsed each parcel's nested `taxAssessments` field to pull the most recent county-assessed value (not sale price, which is recorded for under 2% of parcels and was excluded).
- Normalized inconsistent `ownerOccupied` values (mixed `True`/`False` strings and `"1.0"`/`"0.0"` strings) into a clean boolean.
- Computed a floor-area ratio (finished sqft ÷ lot sqft) as a simple density proxy.
- Aggregated everything by period, by zip, and by individual year — counts, means/medians for size/bedrooms/bathrooms/value, histograms for square footage and lot size, bedroom-count distributions, zoning mix, and owner-occupancy rates.
- Exported the aggregates plus a full point-level table (lat/long, sqft, bedrooms, year, zip, period) to JSON.

**Front end:** A single self-contained HTML file — no build step, no server. Charts are rendered with [Chart.js](https://www.chartjs.org/) (loaded from jsDelivr). The hero map is a hand-built canvas scatter plot (not a tile-based map) plotting every parcel by its actual latitude/longitude, colored by period, with click-free hover tooltips and period/zip filters. All aggregated data is embedded directly in the page as JSON, so the file works completely offline once downloaded.

**Design:** A desert-survey/parcel-ledger aesthetic — warm caliche-tan background, rust (2010–2014) vs. turquoise (2020–2025) as the consistent two-period color code throughout every chart, Space Grotesk for headers, IBM Plex Sans/Mono for body and data labels.

---

## Key findings

### 1. Construction collapsed overall, and the geography flipped
Total new single-family homes across the three zips fell from 8,487 (2010–2014) to 1,855 (2020–2025) — a 78% drop. But the decline was wildly uneven:

| Zip | Area | 2010–2014 | 2020–2025 | Change |
|---|---|---|---|---|
| 79938 | Tierra del Este / far east | 6,998 | 500 | **‑93%** |
| 79911 | Canutillo / Vinton, far northwest | 1,050 | 1,067 | +2% |
| 79936 | East-Central / Pebble Hills | 439 | 288 | ‑34% |

79938 supplied 82% of all new construction in 2010–2014; by 2020–2025 that had fallen to 27%, while 79911 — nearly flat in raw volume — now accounts for 58% of everything being built. The far-east master-planned boom of the early 2010s has effectively ended, and growth has shifted to the far northwest.

### 2. A striking timing anomaly in 79938
Breaking 2020–2025 down year by year, 79911 and 79936 both built homes in every year of the window. **79938 shows zero recorded homes from 2020 through 2024, then all 500 of its homes recorded in 2025 alone.** This could reflect a genuine multi-year construction pause followed by a 2025 rebound, or a lag in how the county dated this batch of records. It's flagged as a data caveat in the dashboard rather than treated as a firm conclusion.

### 3. Homes got bigger, denser (in places), and more expensive
- Average square footage: 1,811 → 2,202 sqft (+22%)
- Average bedrooms: 3.50 → 3.92; bathrooms: 2.41 → 2.62
- Average assessed value: $267,655 → $430,141 (+61%); value per sqft: $151.89 → $188.10 (+24%)

### 4. The three zips are building different products
- **79911** builds the largest, priciest homes in both periods ($179–$195/sqft) and grew bedroom counts the fastest.
- **79936** saw lot sizes grow faster than house size (density ratio fell slightly) — more consistent with spacious infill than compact building.
- **79938** is the opposite: lot sizes *shrank* (7,052 → 5,793 sqft) while bedroom counts rose, pushing its density ratio up — even as its volume collapsed, its remaining product got more compact.

### 5. Ownership is stable; some fields are too sparse to trust
Owner-occupancy sits around 90–92% in 79911 and 79936 in both periods, but there's no occupancy data at all for 79938's 2020–2025 homes. Zoning codes are missing for 65% of all 2020–2025 parcels, so zoning was excluded as a basis for any conclusion. Assessed-value coverage is also incomplete for the newest construction (notably absent for all of 79938's 2020–2025 homes and all of 79936's 2010–2014 homes) — value comparisons should be read as directional, not exhaustive.

---

## Files

- `el_paso_housing_analysis.html` — the full interactive dashboard (this is what's deployed to the GitHub Pages link above)
- `el_paso_housing_data.json` — the standalone processed/aggregated dataset, if you want the numbers without the front end

## Caveats worth repeating

- Only 2010–2014 and 2020–2025 are represented; **2015–2019 is not in the source data**.
- "Estimated value" = most recent county tax assessment on file, not a sale price or market estimate.
- Sample sizes get small at the zip/year level (e.g., 79936 in some years is under 50 homes) — treat single-year, single-zip figures as illustrative, not statistically robust.
