# Methodology

[← Back to main README](../README.md)

This page documents the calculations that are implemented directly in the workbook and separates them from vendor-supplied fields whose internal methodology is not visible in the file.

## Price and volume

### Returns

The dashboard reports 1-, 5- and 30-trading-row returns:

~~~text
Return(n) = Close / Close[n rows ago] - 1
~~~

### Volume ratio

The volume ratio compares current volume with a 10-day average that includes the current day:

~~~text
Volume Ratio = Today's Volume × 10 / Sum(last 10 daily volumes)
~~~

Values above 1 indicate volume above the 10-day average.

### Dollar volume

~~~text
Dollar Volume ($mm) = Close × Volume / 1,000,000
~~~

## Realized volatility

Daily log returns are:

~~~text
r_t = LN(Close_t / Close_(t-1))
~~~

Realized volatility is annualized using:

~~~text
RV(n) = STDEV.S(last n daily log returns) × √252
~~~

The workbook calculates RV5, RV10, RV21, RV30 and RV63.

### RV21 percentile

Current RV21 is ranked against the last 252 RV21 observations using PERCENTRANK. The window includes the current observation.

### RV21 z-score

The in-house volatility score uses a 50-day RV21 z-score:

~~~text
z_RV21 = (RV21 - mean(RV21, 50d)) / stdev(RV21, 50d)
~~~

### RV5 vs RV21

~~~text
RV5 vs RV21 = RV5 - RV21
~~~

Positive values indicate recent realized volatility is running above the 21-day measure.

### Short-term RV trend

~~~text
Trend_10_21 = RV10 / RV21 - 1
~~~

The percentile of this series over 252 observations feeds the in-house VOL score.

## ATR-normalized price extension

The workbook measures how far price is from moving-average references in ATR terms, then z-scores the extension series against recent history.

### Short-term extension

~~~text
Extension_10 = (Close - EMA10) / ATR14
~~~

The resulting series is z-scored over the last 252 valid values and drives the ST score.

### Long-term extension

The 50-day component is:

~~~text
Extension_50 = (Close / SMA50 - 1) / (ATR14 / Close)
~~~

A 200-day SMA extension is also calculated. Their standardized values are averaged for the LT score.

## Relative strength versus SPY

Daily ticker and SPY price changes are each normalized by their own ATR. Each normalized series is accumulated over 50 days, then differenced:

~~~text
RS = Sum50(ΔTicker / ATR_Ticker) - Sum50(ΔSPY / ATR_SPY)
~~~

The dashboard also reports a 63-day percentile of this RS series.

## Implied volatility and skew

The following fields are supplied by the Options Samurai Excel add-in rather than calculated internally:

- approximately 30-day IV;
- IV Rank / IV percentile field;
- volatility risk premium;
- IV-RV percentile;
- Skew Rank;
- put-skew percentile;
- call-skew percentile;
- vendor VOL score;
- vendor volume-power score.

The workbook itself does not document the vendor's exact lookbacks or raw skew construction. These fields should therefore be described as vendor-supplied rather than reverse-engineered facts.

## Volatility risk premium

The dashboard's VRP field is vendor-supplied. A workbook spot-check on SPY was consistent with approximately:

~~~text
VRP ≈ IV - RV30
~~~

but that construction was not independently verified across the full universe. The in-house RV21 measure is a separate calculation and should not be assumed to be the vendor's realized-volatility input.

## Scoring

### Vendor VOL

~~~text
Vendor VOL Score = vendor stock_vol_score × 10
~~~

Exact vendor methodology is not documented.

### In-house VOL

~~~text
In-house VOL =
10 × [0.4 × Φ(RV21 z-score over 50d)
      + 0.6 × Percentile(RV10/RV21 - 1 over 252d)]
~~~

Higher values mean RV21 is elevated versus its recent 50-day norm and/or shorter-horizon RV is increasing relative to RV21.

### Short-term score

The ST score linearly maps the 10-day EMA extension z-score to a 1–10 scale:

~~~text
ST = clip(1 + 9 × ((z + 2) / 4), 1, 10)
~~~

This gives approximately:

- z = -2 → 1
- z = 0 → 5.5
- z = +2 → 10

### Long-term score

LT uses the same mapping on the average of the 50-day and 200-day standardized extension measures.

### Volume score

~~~text
Volume Score = clip(vendor stock_volume_power × 10, 1, 10)
~~~

### Important interpretation

A higher score is not automatically “better.” ST and LT are **extension scores**. A high score can mean strength, over-extension, or both depending on the rest of the context.

## Two-state volatility regime filter

The regime block is not a fitted HMM. It is a two-state Gaussian forward filter with parameters re-estimated from the available return history.

The calculation:

1. computes daily log returns;
2. computes rolling 22-day annualized RV;
3. splits the RV sample around its median;
4. estimates low-state and high-state persistence from empirical transitions;
5. defines low/high state volatility from the 20th/80th RV percentiles;
6. runs a forward probability recursion using zero-mean Gaussian return densities;
7. reports P(HighVar) as one minus the latest low-state filtered probability;
8. combines the volatility state with an EMA9 vs SMA21 trend flag.

A P(HighVar) cutoff of 0.30 produces LOWVAR or HIGHVAR. The trend flag produces BULL or BEAR.

The resulting labels are BULL_LOWVAR, BULL_HIGHVAR, BEAR_LOWVAR and BEAR_HIGHVAR.

The latest regime output is filtered rather than retrospectively smoothed, but the state parameters are estimated using the full available sample through the current date. Historical backfills would therefore require point-in-time parameter estimation to avoid look-ahead.

## Chart definitions

### Volatility Positioning Map

- X = IV Rank
- Y = Skew Rank
- midlines = 50% / 50%

The chart describes relative option-volatility level and smile shape.

### IV Premium Compass

- X = RV21 percentile
- Y = VRP
- midlines = 50% and zero

The chart separates realized-volatility state from the implied-versus-realized premium.

### Reversion Compass

- X = LT extension score
- Y = vendor VOL score
- midlines = 5 / 5

Despite the name, no explicit reversion signal is implemented.

## Fixed-strike volatility

The fixed-strike tracker compares live IV with stored IV at the same expiry and strike:

~~~text
Fixed-Strike Vol Change = Current IV - Snapshot IV
~~~

This helps distinguish actual surface repricing from the mechanical effect of spot moving to a different location on the smile.
