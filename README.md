# MMSDS Adapted Project

### Forecasting UK inflation with autoregressive models and Bank of England data (Time Series)

---

Inflation is one of those things everyone has an opinion about but few people try to model from scratch. This project does that, starting with the raw Consumer Price Index from the ONS and some yield curve data from the Bank of England, we build up a forecasting pipeline from first principles using classical time series methods.

The whole thing lives in a single Jupyter notebook that is framed as a walk-through.

## What it does

The notebook starts with the CPI All Items index (ONS series D7BT, 2015=100), covering January 2010 to January 2026. That window spans the post-crisis recovery, Brexit, COVID, and the 2022-23 energy shock... a lot for one column of numbers.

We fit a linear trend and subtract it, examine the ACF and PACF of the residuals to choose a lag order, and settle on an AR(2) model. The choice is cross-checked with RMSE across a range of lag orders on both training and held-out test data.

The second half switches to monthly inflation rates and brings in the Bank of England's 10-year gilt-implied inflation forward rate (series IUMAMIIF) as an exogenous regressor. Cross-correlation suggests the strongest link is contemporaneous, so we include it at lag zero. We fit an ARX(2) and then an ARMAX(2,1), comparing both against the baseline using walk-forward evaluation.

## Data

**CPI**: ONS series D7BT, downloaded from [ons.gov.uk](https://www.ons.gov.uk/economy/inflationandpriceindices/timeseries/d7bt/mm23). File: `series-050326.csv`. The format is a bit unusual: metadata rows at the top, then annual, quarterly, and monthly figures mixed together. The notebook filters to monthly with a regex.

**Gilt-implied inflation**: Bank of England series IUMAMIIF, from the [Interactive Analytical Database](https://www.bankofengland.co.uk/boeapps/database/). File: `results.csv`. You can also grab it directly via [this URL](https://www.bankofengland.co.uk/boeapps/database/_iadb-fromshowcolumns.asp?csv.x=yes&Datefrom=01/Jan/2010&Dateto=01/Jan/2026&SeriesCodes=IUMAMIIF&CSVF=TN&UsingCodes=Y&VPD=Y&VFD=N).

Both are freely available for non-commercial use.

## Running it

Python 3.8+ with pandas, numpy, matplotlib, scikit-learn, and statsmodels. Put the two CSV files in the same folder as the notebook and run all cells. No API keys, no containers, nothing clever.

## Caveats

The gilt-implied rate tracks RPI expectations, not CPI index-linked gilts are indexed to RPI, which runs about 0.5-1pp higher due to the formula effect. The model coefficients partially absorb this, but it is a systematic bias worth knowing about.

The 10-year rate reflects average expected inflation over a decade. Comparing that to month-on-month CPI changes is a stretch as a shorter maturity would be more responsive, but the 10-year series has better availability and seems to be the standard benchmark.

The Bank of England forecasts inflation with BVARs, DSGE models, and judgement-augmented fan charts. This notebook covers the building blocks those methods are built from.
