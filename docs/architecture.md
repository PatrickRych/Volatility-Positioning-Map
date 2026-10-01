# Workbook Architecture

[← Back to main README](../README.md)

## Overview

The workbook is built as a 25-name cross-sectional volatility research system. Price history is calculated on per-ticker sheets, aggregated into the main dashboard, combined with option-volatility fields, then routed into filtered tables and chart helpers.

## Sheet map

| Sheet | Role | Main purpose |
|---|---|---|
| Dashboard | User-facing | Main screener grid, scores, regime outputs and charts |
| Screener_Filter | User-facing / helper | Live mirror of the dashboard rows for sorting and filtering |
| Chart_Data | Calculation / helper | Pulls chart fields and constructs scatter-map backgrounds and midlines |
| Pivot_ScreenerFilter | Helper | Pivot tables supporting IV Rank / Skew Rank bar charts; requires refresh |
| Data_Aggregate(T1-T10) | Calculation | Collects summary values from all 25 ticker-history sheets despite the legacy name |
| T1–T25 | Data + calculation | Daily price/volume history, rolling indicators, realized-volatility statistics and latest-value summaries |
| Markov_Params | Input | Named parameters used by the two-state volatility regime filter |
| FIXED-STRIKE-VOL | User-facing | Single-ticker fixed-strike IV snapshot comparison |
| Skew_Lookup | Reference | Static skew/volatility reference information; not confirmed as wired into the live dashboard |
| Master Stat Table | Reference | Static options-strategy notes; not connected to live calculations |
| IV_Straddle_Explainer | Reference | Educational reference with a broken link to a missing sheet |
| Color Scheme | Reference | Dashboard palette |
| History_5Y | Stub | Unused placeholder |
| Claude Log | Admin | AI-edit log; should not be part of a public demo workbook |
| Sheet9 | Empty | Unused |

## Main data flow

~~~mermaid
flowchart TD
    A[Dashboard ticker inputs] --> B[T1–T25 ticker sheets]
    C[Microsoft STOCKHISTORY] --> B
    B --> D[Returns, ATR, moving averages, RV, RS, percentiles, z-scores]
    D --> E[Latest-value summary blocks]
    E --> F[Data_Aggregate]
    F --> G[Dashboard]
    H[Options-volatility vendor fields] --> G
    I[Markov parameters] --> G
    G --> J[Screener_Filter]
    J --> K[Chart_Data]
    K --> L[Positioning / Premium / Reversion scatter maps]
    J --> M[Pivot tables]
    M --> N[IV Rank / Skew Rank bar charts]
    H --> O[FIXED-STRIKE-VOL]
~~~

## Ticker-history layer

Each ticker sheet receives the ticker symbol from the dashboard and uses daily price-history formulas to build approximately 1,000 calendar days of history. The calculation layer includes:

- close-to-close log returns;
- moving averages and EMA state;
- Wilder ATR14;
- realized volatility at 5, 10, 21, 30 and 63 trading days;
- ATR-normalized price extension;
- relative strength versus SPY;
- percentile and z-score normalization;
- a latest-value summary block used by the aggregate sheet.

The public documentation assumes the T2–T25 sheets follow the T1 formula pattern described in the technical handoff; the original audit did not inspect every cell on every T-sheet.

## Dashboard layer

The dashboard has a hard-coded 25-row structure. It pulls price-derived values from Data_Aggregate and option-volatility fields directly from the vendor add-in. The two-state regime formula is recalculated from the corresponding T-sheet close history.

The dashboard then produces five visible score columns:

- vendor VOL;
- in-house VOL;
- short-term extension score;
- long-term extension score;
- vendor volume-power score.

There is no composite score.

## Chart layer

The three scatter maps are fed from Screener_Filter through Chart_Data and update with the live table. The separate IV Rank and Skew Rank bar charts are PivotTable-driven and may remain stale until refreshed.

## Fixed-strike layer

FIXED-STRIKE-VOL is a standalone workflow. It locks a small set of expiries, evaluates IV at fixed strikes, and compares live IV with manually stored snapshot slots. This is intended to distinguish true fixed-strike repricing from apparent IV changes caused by moving along the smile as spot changes.

## Structural constraints

The current workbook is not dynamically scalable. Important hard-coded elements include:

- 25 ticker rows;
- per-row references to specific T-sheets;
- fixed history ranges;
- fixed chart ranges in the fixed-strike tracker;
- manually committed snapshot slots.

These are appropriate for a research prototype but should be refactored before presenting the workbook as a general-purpose production application.
