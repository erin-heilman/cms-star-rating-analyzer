# Troubleshooting Guide

## Common Issues

### File Upload Issues

#### "Please upload a CSV file from your Hospital Specific Report"

**Cause**: The uploaded file does not have a `.csv` extension.

**Solution**:
- Ensure you are uploading the CSV version of the HSR, not an Excel file (.xlsx or .xls)
- If you received an Excel file, open it in Excel and use **File > Save As > CSV (Comma delimited) (*.csv)**
- Make sure the file extension is `.csv`, not `.CSV` (case should not matter, but rename if needed)

#### "Error processing CSV file. Please ensure it's a valid HSR file."

**Cause**: The CSV file structure doesn't match the expected HSR format.

**Solutions**:
- Verify you are uploading a **Hospital Specific Report** CSV from CMS QualityNet, not a different CMS data file
- The HSR CSV should have exactly 2 rows: row 1 is headers, row 2 is values
- Open the CSV in a text editor to confirm it is comma-delimited and not semicolon-delimited or tab-delimited
- If the file was modified in Excel and re-saved, Excel may have altered the formatting. Try uploading the original unmodified CSV

#### File uploads but nothing displays

**Cause**: The CSV may be from a different star rating period or use unexpected column names.

**Solutions**:
- This tool is designed for the **April 2026** Hospital Specific Report. Earlier HSR files (e.g., July 2025) may use different column naming conventions
- Check the browser console (F12 > Console tab) for specific JavaScript errors
- Verify the CSV contains columns starting with `Measure_Scores-`, `Measure_Group_Scores-`, and `Star_Rating_Results-`

---

### Display Issues

#### All scores show as "0.000" or "N/A"

**Cause**: Column name mismatch between the CSV and what the tool expects.

**Solutions**:
- The April 2026 HSR uses column names without the `_f` suffix (e.g., `nat_mean` instead of `nat_mean_f`). The tool supports both formats, but if CMS introduces a third format, the column names in `extractData()` will need updating
- Open the CSV in a text editor and search for `nat_mean` to see which format your file uses

#### Star rating shows "3 out of 5 stars" but my hospital has a different rating

**Cause**: The tool defaults to 3 stars if it cannot parse the star rating from the CSV.

**Solution**:
- Check that the CSV contains a column named `Star_Rating_Results-HOSP_RESULT-Star_Score`
- The value should be in the format `**** (4 out of 5 stars)` — the tool extracts the first number it finds

#### Peer group shows "5 Measure Groups" but my hospital has fewer

**Cause**: Same defaulting behavior — the tool defaults to 5 if it cannot read the peer group.

**Solution**:
- Verify the CSV has a column named `Star_Rating_Results-HOSP_RESULT-peer_group`
- Expected values: `3 Measure Groups`, `4 Measure Groups`, or `5 Measure Groups`

#### Measure group header text disappears on hover (Individual Measures tab)

**Status**: Fixed. The blue header rows for each measure group (e.g., "Mortality", "Safety of Care") now maintain their blue background on hover, keeping the white text readable.

If you still see this issue, hard-refresh the page (Cmd+Shift+R on Mac, Ctrl+Shift+R on Windows) to clear the browser cache.

#### Quartile ranking bars don't show for a measure group

**Cause**: The hospital has 0 reported measures in that group.

**Solution**: This is expected behavior. If a hospital has no measures in a group, the quartile bar is intentionally hidden since there is no score to display.

---

### Calculation Discrepancies

#### My summary score doesn't match what the tool calculates

**Explanation**: The tool calculates the summary score as the weighted sum of standardized group scores. Small rounding differences (typically < 0.01) can occur between the tool's calculation and CMS's value because:
- CMS uses R for calculations with higher precision
- The tool displays and sums values rounded to 3 decimal places

**Verification**: The CSV's authoritative summary score is in the `Star_Rating_Results-HOSP_RESULT-summary_score` column. The tool displays this value directly in the Overview tab.

#### The quartile ranking seems wrong for my hospital

**Explanation**: The quartile rankings compare the hospital's **measure group score** (the raw simple average of z-scores in that group) against national benchmarks — NOT the standardized group score. Specifically:

- `grp_score` (the raw group score) is compared against the Table 7 percentile benchmarks
- `hosp_std_grp_score` (the standardized group score) is what goes into the summary score calculation

These are different numbers. The quartile visualization uses `grp_score` because Table 7 benchmarks are based on the distribution of raw group scores.

#### A measure shows "Above" performance but has a negative z-score (or vice versa)

**Explanation**: Performance badges use these thresholds:
- **Above**: z-score > 0.5
- **Average**: z-score between -0.5 and 0.5
- **Below**: z-score < -0.5

A z-score of -0.3 would show "Average" even though it's below the national mean. These thresholds are built into the tool for simplicity — they are not CMS-defined cutoffs.

---

### Deployment Issues

#### Vercel deploy fails

**Solutions**:
- Ensure you are logged in: `vercel whoami`
- Ensure you are scoped to the correct team: `vercel link --scope medisolv`
- The project has no build step — Vercel should detect it as a static site automatically
- If Vercel prompts for build settings, use:
  - Framework: None / Other
  - Build Command: (leave empty)
  - Output Directory: `.`
  - Install Command: (leave empty)

#### Changes pushed to GitHub but Vercel doesn't update

**Solutions**:
- Check if the Vercel project is connected to the GitHub repo: go to Vercel Dashboard > Project > Settings > Git
- If not connected, you can either:
  - Connect it via the Vercel dashboard
  - Manually deploy with `vercel --prod`
- Check the Vercel deployment logs for errors: Vercel Dashboard > Project > Deployments

#### Two Vercel projects exist — which is which?

| Version | Vercel Account | URL | GitHub Connection |
|---|---|---|---|
| 2025 (old) | eheilman-5631 (personal) | cms-star-rating-analyzer.vercel.app | Connected to main branch (old code) |
| 2026 (current) | Medisolv (enterprise) | cms-star-rating-analyzer-sandy.vercel.app | Manual deploy |

If you want the Medisolv project to auto-deploy from GitHub, connect it via the Vercel dashboard under the Medisolv team.

---

### Browser Issues

#### Tool doesn't work in Internet Explorer

**Explanation**: This tool uses modern JavaScript features (nullish coalescing `??`, template literals, `Array.from`, arrow functions) that are not supported in IE. Use Chrome, Firefox, Safari, or Edge.

#### Large CSV files take a long time to load

**Explanation**: HSR CSV files are typically small (one row of data), so this should not be an issue. If you are uploading a multi-hospital dataset or a different file type, that is not supported — this tool is designed for single-hospital HSR files only.

---

## Getting Help

1. **Check the browser console**: Press F12 (or Cmd+Option+I on Mac) and look at the Console tab for JavaScript errors
2. **Verify the CSV**: Open the CSV in a text editor to confirm it has the expected column structure
3. **Compare against CMS documentation**: The [HSR User Guide](https://qualitynet.cms.gov) explains each column in the CSV
4. **Review the Developer Guide**: See [DEVELOPERS.md](DEVELOPERS.md) for code architecture details
