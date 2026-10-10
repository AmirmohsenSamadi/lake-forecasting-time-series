# Report Summary: Smart Water Management, Lakes

## Objective
Forecast lake water level and inflow rate from historical weather data, and compare classical time-series models of increasing complexity: AR, MA, ARMAX and SARIMAX.

## Data
- 6,603 daily records from July 2006 to 2024: mean rainfall, mean temperature, inflow rate and lake level.
- No missing values, duplicates or type errors. The integer day index was converted to dates.

## Exploratory analysis
- **Seasonality:** inflow and rainfall peak in winter, temperature peaks in summer, and the lake level is stable with mild seasonal swings. There is no long-term trend from 2006 to 2024.
- **Memory:** temperature and lake level show long autocorrelation. Rainfall is close to random, and inflow has short-term dependence (PACF significant at lags 1–2).
- **Distributions:** rainfall and inflow are strongly right-skewed. A Kolmogorov–Smirnov test rejects normality for all variables.
- **Stationarity:** ADF, KPSS and Phillips–Perron tests agree that the series are stationary.
- **STL decomposition:** clear annual seasonality. Inflow residuals contain sharp storm-related spikes.
- **Correlations:** lake level correlates with inflow (+0.34) and negatively with temperature (−0.39), suggesting an evaporation effect. Rainfall and inflow correlate only weakly on the same day (+0.05), which points to a delay between rain and inflow.

## Preprocessing
- Holt–Winters exponential smoothing (additive trend and seasonality) to reduce noise.
- Chronological 80/20 train/test split, with standardisation fitted on the training data only to prevent leakage.

## Models
- **AR / MA:** univariate baselines with lags selected from PACF/ACF and information criteria.
- **ARMAX:** autoregressive and moving-average terms plus exogenous drivers (rainfall, temperature and the other hydrological variable), on monthly data.
- **SARIMAX(2,0,2)(1,0,1)₁₂:** ARMAX plus a yearly seasonal component.

## Results (test set)
| Target | Model | MAE | RMSE | MAPE |
|---|---|---:|---:|---:|
| Lake level | AR | 0.851 | 1.044 | 0.34% |
| Lake level | MA | 0.933 | 1.187 | 0.37% |
| Lake level | ARMAX | 1.02 | 1.18 | 0.41% |
| Lake level | **SARIMAX** | **0.39** | **0.50** | **0.16%** |
| Inflow rate | AR | 1.861 | 3.055 | 312.48% |
| Inflow rate | MA | 1.871 | 3.056 | 315.58% |
| Inflow rate | ARMAX | 1.61 | 2.08 | 81.51% |
| Inflow rate | **SARIMAX** | **1.23** | **1.68** | **55.69%** |

## Conclusions
- SARIMAX is the best model for both targets, because it combines seasonality with external weather drivers.
- Simple univariate models capture the average behaviour but fail on sudden inflow surges, which shows in their very high relative errors.
- Including exogenous variables and seasonal structure is essential when modelling environmental systems.
