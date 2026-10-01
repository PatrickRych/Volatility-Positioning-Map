# Limitations & Validation Gaps

[← Back to main README](../README.md)

The model is a research prototype. The limitations below are part of the project documentation rather than hidden implementation details.

## Data and history

- **Short histories:** newer products may not have a complete 252-observation window. DRAM was specifically identified as having insufficient history for parts of the model.
- **Mixed asset classes:** equity ETFs, commodity products, crypto ETFs and UVIX do not share the same structural volatility behavior.
- **SPY relative strength:** SPY is its own benchmark, so its RS value is mechanically zero.
- **Crypto weekend gaps:** close-to-close ETF history does not capture all underlying weekend crypto movement.
- **Vendor methodology:** exact construction/lookback for IV Rank, Skew Rank, vendor VOL and volume-power fields is not documented in the workbook.

## Statistical choices

- 252-observation percentiles depend on the chosen window and include the current observation.
- Z-scores are sensitive to the selected 50- or 252-observation history.
- ST and LT scores are transformations of extension, not validated forecasts.
- The model has no documented out-of-sample backtest establishing predictive edge.

## Regime model

- The regime engine is not a fitted HMM.
- State parameters are re-estimated using the full available sample through the current date.
- The HIGHVAR cutoff of 0.30 is heuristic.
- The dashboard trend flag and regime trend flag use slightly different moving-average definitions.
- Historical regime backfills would need point-in-time parameter estimation to avoid look-ahead.

## Refresh and as-of risks

- Pivot-based IV Rank and Skew Rank bar charts can remain stale until PivotTables are refreshed.
- Price-derived values can reflect the prior close while vendor fields are intraday.
- Users should verify as-of dates before comparing rows or capturing screenshots.

## Silent error handling

IFERROR is used widely. Missing or failed values can become blanks, and Chart_Data can convert blanks into #N/A so points disappear from charts without a prominent error message.

A production implementation should surface explicit data-quality flags.

## Hard-coded structure

The workbook has a 25-ticker limit and fixed ranges. Some dashboard rows reference specific T-sheets rather than dynamically mapping a scalable table.

The fixed-strike tracker also uses fixed strike/chart ranges and manual snapshot slots.

## Known workbook cleanup items

The technical handoff identified several items to repair before publishing a downloadable workbook:

- BOTZ regime output is hard-coded as “No T-sheet data” despite an available ticker sheet;
- two score columns share the same VOL header;
- a legacy dashboard block contains broken references;
- several named ranges are broken or unused;
- History_5Y and Sheet9 are unused;
- IV_Straddle_Explainer points to a missing sheet;
- fixed-strike chart titles contain stale dates;
- fixed-strike slot-selection logic should be reviewed;
- a suspicious fixed-strike spot-marker reference should be checked;
- Master Stat Table contains third-party research claims that should be independently sourced or removed.

## Audit scope

The source audit inspected the main dashboard, data-aggregate logic, T1 formulas and selected settings across T1–T25, Markov parameters, chart data, the screener table, pivot extremes, scatter-series definitions and the fixed-strike sheet.

It did **not** audit every cell in every T-sheet or fully verify every pivot-chart source. Those items remain validation tasks before claiming complete independent verification.
