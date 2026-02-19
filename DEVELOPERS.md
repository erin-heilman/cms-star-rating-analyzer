# Developer Guide

This guide covers the architecture, code structure, and key concepts for developers working on the CMS Hospital Star Rating Analyzer.

## Architecture

The entire application is a **single HTML file** (`index.html`) with no external dependencies, no build step, and no framework. Everything — HTML structure, CSS styles, and JavaScript logic — is self-contained.

### Why a Single File?

- **Zero dependencies** means zero supply chain risk and no `node_modules`
- Deploys as a static file to Vercel with no build configuration
- Easy to inspect, debug, and hand off
- The application is client-side only — no data leaves the browser

### File Structure

```
index.html
  ├── <style> block        (lines 7–535)     — All CSS
  ├── <body> HTML          (lines 537–636)    — Page structure and tab layout
  └── <script> block       (lines 641–end)    — All JavaScript logic
```

## CSS Architecture

### Design Tokens (CSS Custom Properties)

All colors are defined as CSS variables in `:root` (lines 14–32):

| Variable | Value | Usage |
|---|---|---|
| `--primary` | `#00539F` | Headers, buttons, active states (Medisolv blue) |
| `--primary-dark` | `#003d75` | Hover states for primary elements |
| `--secondary` | `#00B5E2` | Upload button, accent highlights |
| `--success` / `--accent` | `#7ED321` | Good performance indicators |
| `--warning` | `#FDB913` | Average performance indicators |
| `--danger` | `#ED1C24` | Poor performance indicators |
| `--gray-50` through `--gray-900` | Grayscale | Backgrounds, borders, text |

### Key CSS Classes

- `.data-table` — Styled table wrapper with rounded corners and header styling
- `.measure-group-header` — Blue header rows for measure group sections in the Individual Measures tab. Note: has a specific `:hover` override to prevent white-text-on-white-background
- `.performance-badge` — Colored pill labels (`.performance-good`, `.performance-average`, `.performance-poor`)
- `.threshold-box` — Star rating threshold cards; `.current` highlights the hospital's star level
- `.star-display` — Gradient hero section showing the overall star rating

### Responsive Design

Media query at `768px` switches to single-column layout. The `.tabs` bar stacks vertically, grid layouts collapse, and threshold boxes go single-column.

## JavaScript Architecture

### Global State

```javascript
let hospitalData = {};    // Raw key-value pairs from CSV
let measureGroups = {};   // Processed measure group data (5 groups)
let measures = [];        // Array of all 52 individual measure objects
```

### Constants

#### `GROUP_PERCENTILE_BENCHMARKS` (Table 7)

National distribution data for each measure group. Used by the quartile ranking visualization on the Summary Scores tab.

```javascript
{
  'Mortality': { n: 4133, nationalAvg: -0.079, stdDev: 0.7382,
                 min: -6.404, p25: -0.498, median: -0.030, p75: 0.392, max: 3.014 },
  // ... (Safety of Care, Readmission, Patient Experience, Timely & Effective Care)
}
```

**Source**: Comprehensive Methodology v5.0, Table 7

#### `PEER_GROUP_THRESHOLDS` (Table 8)

K-means clustering score ranges for each star level within each peer group. Used to show the hospital which score range corresponds to each star.

```javascript
{
  '3 Measure Groups': { count: 177, ranges: [{ min, max }, ...] },  // 5 ranges for 1-5 stars
  '4 Measure Groups': { count: 749, ranges: [...] },
  '5 Measure Groups': { count: 2277, ranges: [...] }
}
```

**Source**: Comprehensive Methodology v5.0, Table 8

#### `MEASURE_NAMES`

Maps each measure ID to its display name. Contains all 52 measures organized by group in comments.

### Key Functions

#### CSV Processing Pipeline

1. **`handleFile(file)`** — Validates file is CSV, shows loading spinner, reads file content
2. **`processCSV(content)`** — Parses CSV into `hospitalData` key-value object. The CSV is a 2-row format: row 1 = headers, row 2 = values
3. **`extractData()`** — Populates `measureGroups` and `measures` from `hospitalData`
4. **`displayResults()`** — Orchestrates rendering across all 4 tabs

#### Data Extraction (`extractData`)

**Measure Groups** (lines ~910–970): Reads group-level data from CSV columns like:
- `Measure_Group_Scores-{GroupName}-grp_score` — Raw group score
- `Measure_Group_Scores-{GroupName}-hosp_std_grp_score` — Standardized group score
- `Measure_Group_Scores-{GroupName}-hosp_weight` — Applied weight
- `Measure_Group_Scores-{GroupName}-grp_natmean` — National mean for the group
- `Measure_Group_Scores-{GroupName}-grp_stdev` — Standard deviation for the group

**Individual Measures** (lines ~975–995): For each measure ID in `MEASURE_NAMES`, reads:
- `Measure_Scores-hosp_meas_result-{ID}` — Hospital's raw result
- `Measure_Scores-nat_mean-{ID}` — National average
- `Measure_Scores-hosp_std-{ID}` — Standard deviation
- `Measure_Scores-hosp_meas_std-{ID}` — Z-score (standardized measure score)
- `Measure_Scores-hosp_meas_weight-{ID}` — Measure weight within the group

**Backward Compatibility**: The code uses nullish coalescing (`??`) to support both the 2026 column format (no suffix) and the older 2025 format (`_f` suffix):
```javascript
const nationalMean = hospitalData[`Measure_Scores-nat_mean-${id}`]
                  ?? hospitalData[`Measure_Scores-nat_mean_f-${id}`];
```

#### Display Functions

- **`displayOverview()`** — Star display, peer group thresholds, quick stats, measure group bars
- **`displaySummaryScores()`** — Score table and national quartile ranking bars
- **`displayMeasures()`** — Individual measure tables grouped by category
- **`displayInsights()`** — Path to next star, improvement opportunities, strengths

#### Helper Functions

- **`getMeasureGroup(measureId)`** — Routes a measure ID to its group name using prefix matching
- **`getQuartileInfo(groupName, score)`** — Returns quartile label, CSS class, and description based on Table 7 benchmarks
- **`safeNumber(value, defaultValue)`** — Safely parses a number, returning default if NaN
- **`formatNumber(value, decimals)`** — Formats a number to specified decimal places, handling N/A

### Quartile Bar Visualization Logic

The quartile bars on the Summary Scores tab divide the bar into **4 equal-width zones** (25% each), then position the hospital marker within its zone proportionally:

```
[  Bottom 25%  |  Below Avg  |  Above Avg  |   Top 25%  ]
0%            25%           50%           75%          100%
```

The marker position within each zone is calculated by mapping the hospital's score between the zone's boundaries (e.g., p25 to median for "Below Average") to the pixel range of that zone.

## Updating for Future Star Rating Releases

When CMS publishes a new Star Rating (e.g., October 2026 or April 2027), the following sections need to be updated:

### 1. Measure List (`MEASURE_NAMES`)
- Add any new measures
- Remove any retired measures
- Update any renamed measure IDs

### 2. Group Routing (`getMeasureGroup`)
- Ensure any new measure IDs route to the correct group

### 3. Peer Group Thresholds (`PEER_GROUP_THRESHOLDS`)
- Update from the new Comprehensive Methodology's Table 8
- Update hospital counts per peer group from Table 9

### 4. National Percentile Benchmarks (`GROUP_PERCENTILE_BENCHMARKS`)
- Update from the new Comprehensive Methodology's Table 7
- Includes: national average, std dev, min, p25, median, p75, max, and N for each group

### 5. Default Measure Counts
- In `extractData()`, update the fallback `potential` counts for each group

### 6. Total Measure Count
- Search for the old total (currently "52") and update any references

### 7. Info Box Text
- Update the version, date, and rule references in the info box HTML

### 8. CSV Column Names
- If CMS changes column naming conventions again, update the column name patterns in `extractData()`

### Where to Find CMS Source Data

- **QualityNet**: https://qualitynet.cms.gov — HSR User Guide, methodology reports
- **CMS Data Downloads**: https://data.cms.gov/provider-data/search?theme=Hospitals
- **R Code Pack**: Published on QualityNet alongside each star rating release
