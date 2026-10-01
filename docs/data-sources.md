# Data Sources & Publication Notes

[← Back to main README](../README.md)

## Live dependencies

### Microsoft Excel STOCKHISTORY

Used for daily Date, Open, Close, Volume, High and Low history for the ETF universe and SPY benchmark.

The workbook uses this history to calculate returns, ATR, moving averages, realized volatility, relative strength, percentiles and z-scores.

**Publication note:** the repository does not distribute the raw historical dataset. Before releasing a downloadable public workbook, current Microsoft market-data terms should be checked or the history should be replaced with synthetic/static demonstration data.

### Options Samurai Excel add-in

The live workbook uses Options Samurai fields for:

- stock IV;
- IV Rank / percentile;
- IV-RV / volatility risk premium;
- IV-RV percentile;
- Skew Rank;
- put-skew percentile;
- call-skew percentile;
- vendor VOL score;
- vendor volume-power score;
- fixed-strike option IV and expiration fields.

These values are subscription/vendor-derived. Their underlying raw feed is not included in this repository.

The workbook does not document the exact vendor methodology for every field. Public documentation should therefore preserve the distinction between **workbook-derived metrics** and **vendor-supplied metrics**.

## Manual inputs

Manual inputs include:

- the 25 dashboard tickers;
- regime-model parameters;
- certain ticker-sheet parameters;
- fixed-strike locked expiries;
- fixed-strike strike grid;
- saved snapshot slots and labels.

The fixed-strike snapshot values originate from the vendor feed even though they are stored by manual copy/paste.

## Point-in-time timing

The model mixes two timing conventions:

- price-history calculations generally reflect the latest available daily close;
- option-volatility vendor fields can be intraday/live.

That means the same row can combine prior-close price history with current-session implied-volatility readings. The dashboard's as-of date should always be checked before making comparisons.

## Public demo recommendation

A shareable public workbook is feasible, but the cleanest version would:

1. replace live STOCKHISTORY output with synthetic or static permitted history;
2. replace vendor-derived option fields with synthetic/static demonstration values;
3. remove the add-in formulas;
4. preserve the formulas for workbook-derived metrics and scores;
5. remove admin/AI logs and broken legacy references;
6. ship as .xlsx rather than a macro container unless macros are intentionally part of the demo.

The GitHub repository can still document the real architecture while using a sanitized demonstration dataset for reproducibility.
