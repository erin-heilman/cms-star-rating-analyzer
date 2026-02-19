# CMS Hospital Star Rating Analyzer

A browser-based tool that parses CMS Hospital Specific Report (HSR) CSV files and provides a comprehensive analysis of a hospital's Overall Star Rating, including measure-level detail, national quartile rankings, and improvement insights.

**Live URL:** [cms-star-rating-analyzer-sandy.vercel.app](https://cms-star-rating-analyzer-sandy.vercel.app)

## Overview

CMS publishes an Overall Hospital Quality Star Rating for acute care hospitals using a 9-step methodology. Hospitals receive a Hospital Specific Report (HSR) as a CSV file containing their measure scores, national benchmarks, and calculated star rating. This tool makes that data accessible and actionable by:

- Displaying the hospital's overall star rating and summary score
- Showing peer group thresholds so hospitals understand the score ranges for each star level
- Breaking down performance by each of the 5 measure groups with national quartile rankings
- Listing all 52 individual measures with z-scores, national means, and performance indicators
- Identifying the top improvement opportunities and strengths
- Calculating the distance to the next star level

## Current Version

**April 2026 — Methodology v5.0**

This version reflects all changes from the CY 2026 OPPS/ASC Final Rule (CMS-1834-FC):

- **52 total measures** across 5 groups (up from 46 in 2025)
- **New Step 9**: Safety of Care 4-star cap for hospitals in the lowest quartile with 3+ safety measures
- **6 new measures**: Hybrid HWM (Mortality), 5 OAS CAHPS measures (Patient Experience)
- **HCAHPS scoring change**: Measures now use linear mean scores (0-100 scale) instead of star ratings
- **Measure removals**: PC-01 (Elective Delivery) and HCP-COVID-19 retired
- **National quartile benchmarks** from Table 7 of Comprehensive Methodology v5.0

### Measure Groups and Weights

| Group | Weight | Measures |
|---|---|---|
| Mortality | 22% | 8 |
| Safety of Care | 22% | 8 |
| Readmission | 22% | 11 |
| Patient Experience | 22% | 15 |
| Timely & Effective Care | 12% | 10 |

## Application Tabs

### Overview
- Overall star rating with visual star display
- Summary score and peer group assignment
- Star threshold ranges for the hospital's peer group (3, 4, or 5 measure groups)
- Quick statistics (provider ID, measures reported)
- Measure group performance bars

### Summary Scores
- Detailed table with each group's score, national score, weight, and weighted contribution
- **National Quartile Rankings**: Visual bar charts comparing the hospital's measure group scores against national 25th, 50th (median), and 75th percentile benchmarks from CMS Table 7

### Individual Measures
- All 52 measures organized by group
- Each measure shows: hospital result, national mean, standard deviation, z-score, weight, and performance badge (Above/Average/Below)

### Analysis & Insights
- Points needed to reach the next star level
- Top 5 improvement opportunities (worst-performing measures)
- Top 5 strengths (best-performing measures)

## Tech Stack

- **Frontend**: Single HTML file with embedded CSS and JavaScript (no build tools, no dependencies)
- **Hosting**: Vercel (static deployment)
- **Source Control**: GitHub

## Data Sources

All thresholds and benchmarks are sourced from official CMS publications:

- **Table 7** (Comprehensive Methodology v5.0): National percentile benchmarks by measure group
- **Table 8** (Comprehensive Methodology v5.0): K-means star rating score ranges by peer group
- **Table 9** (Comprehensive Methodology v5.0): Hospital distribution counts by peer group
- **Appendix D** (Comprehensive Methodology v5.0): Complete measure list with IDs

## Repository Structure

```
cms-star-rating-analyzer/
  index.html          # Entire application (HTML + CSS + JS)
  .gitignore          # Git ignore rules
  README.md           # This file
  DEVELOPERS.md       # Developer guide (code architecture, key functions)
  SETUP.md            # Setup and installation guide
  TROUBLESHOOTING.md  # Common issues and solutions
```

## Related Documents

- [CMS Overall Star Rating Methodology Results (April 2026)](https://qualitynet.cms.gov)
- [Hospital Specific Report User Guide (April 2026)](https://qualitynet.cms.gov)
- [Star Ratings Comprehensive Methodology v5.0](https://qualitynet.cms.gov)
- [QualityNet FAQs (April 2026)](https://qualitynet.cms.gov)
