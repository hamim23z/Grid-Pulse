# Grid Pulse

*An interactive data story exploring how the U.S. power grid's demand, generation mix, and net load change by hour, season, and year — with a two-period comparison view and statistically grounded trends.*

## Overview

The grid changes constantly: solar pushes midday demand down and steepens the evening ramp, demand tracks temperature, and the generation mix shifts year over year as renewables scale up. This project turns that into an explorable, honest data story — honest meaning every trend is reported with a confidence interval, every chart states its units and source, and every claim about "what changed" is backed by an actual before/after comparison rather than a eyeballed chart.

## Views

| Question | View |
| --- | --- |
| What does a typical day look like each season? | Heatmap of hour-of-day × month (e.g. solar share, net load) — shows the solar "duck curve" |
| How has the generation mix changed over the years? | Stacked area / line chart of generation by fuel |
| What changed between two periods? | Pick two date ranges and regions; see the diff in the average hourly profile, with a table of the biggest changes |
| How does demand respond to temperature? | Scatter of demand vs. temperature with binned medians and a fitted curve |
| Is a trend real, and when did it shift? | Deseasonalized trend with confidence bands, plus detected change points |

## Data

| Source | What it provides |
| --- | --- |
| [EIA Hourly Electric Grid Monitor (EIA-930)](https://www.eia.gov/electricity/gridmonitor/) | Hourly demand, generation by fuel, and interchange, per balancing authority (CAISO, NYISO, ERCOT, and others) |
| [Open-Meteo](https://open-meteo.com/) | Hourly historical temperature, no API key required |

Derived measures (generation share by fuel, net load, an estimated carbon intensity from published per-fuel emissions factors) are computed from these sources and clearly labeled as estimates, not official figures.

## Tech stack

**Pipeline:** Python (pandas/Polars, DuckDB, statsmodels, `ruptures` for change-point detection)
**Frontend:** TypeScript, Next.js, D3 / Observable Plot
**Deployment:** Docker for the pipeline; static hosting (e.g. Vercel) serving pre-aggregated Parquet/JSON — nothing heavy runs in the browser

## Getting started

```bash
git clone https://github.com/<your-username>/grid-pulse.git
cd grid-pulse

# 1. Pipeline: pull and aggregate data
cd pipeline
pip install -r requirements.txt
cp .env.example .env   # add EIA_API_KEY (free, from eia.gov)
python -m pipeline.run

# 2. Web app
cd ../web
npm install
npm run dev
```

## Analysis approach

Findings in the published data story start from a stated hypothesis (for example, "midday solar share in CAISO has grown enough to deepen the net-load minimum and steepen the evening ramp"), are tested against the data with reported effect sizes and confidence intervals, and are published alongside an explicit statement of what the analysis can't show (association vs. causation, data-quality caveats, time-zone handling). See `docs/decisions.md` for data-cleaning notes (missing hours, outliers, daylight-saving handling).

## Project structure

```
grid-pulse/
  pipeline/          # EIA + Open-Meteo loaders, DuckDB tables, aggregation
  web/                # Next.js + D3 frontend, reads pre-aggregated files
  docs/
    decisions.md      # data cleaning and modeling decisions
    findings.md        # the published data story
```
