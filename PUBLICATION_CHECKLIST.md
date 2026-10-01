# Publication Checklist

## Documentation

- [x] Main README built from the workbook technical handoff.
- [x] Architecture documented.
- [x] Methodology and scoring documented.
- [x] Data-source notes documented.
- [x] Limitations and validation gaps documented.
- [x] Current screenshots cropped without changing the underlying values.

## Workbook cleanup before public demo

- [ ] Fix BOTZ regime mapping.
- [ ] Rename the two duplicate VOL score headers.
- [ ] Remove broken legacy dashboard rows.
- [ ] Remove/fix broken named ranges and solver leftovers.
- [ ] Remove Claude Log, Sheet9 and unused History_5Y from the public copy.
- [ ] Fix or remove IV_Straddle_Explainer missing-sheet reference.
- [ ] Remove or independently source/rewrite Master Stat Table third-party claims.
- [ ] Fix fixed-strike tracker titles, slot logic and suspicious references.
- [ ] Refresh all PivotTables and verify rank charts against the live dashboard.

## Data / rights

- [ ] Confirm current Microsoft STOCKHISTORY redistribution terms before releasing raw history.
- [ ] Confirm vendor screenshot/data redistribution rights for a downloadable demo.
- [ ] Replace live/vendor-derived values with synthetic/static demo data if needed.
- [ ] Remove add-in formulas from the public workbook if the vendor data cannot be redistributed.

## Validation

- [ ] Verify vendor definitions/lookbacks for IV Rank, VRP, Skew Rank, vendor VOL and volume power.
- [ ] Verify VRP construction beyond the SPY spot-check.
- [ ] Independently inspect all 25 T-sheets for formula consistency.
- [ ] Confirm pivot-chart sources and refresh behavior.
- [ ] Verify no private data, account data or credentials remain.

## Release

- [x] Create public GitHub repository Volatility-Positioning-Map.
- [x] Upload documentation.
- [ ] Upload curated screenshots.
- [ ] Add sanitized public demo workbook when ready.
- [ ] Add repository to the central portfolio landing page.
