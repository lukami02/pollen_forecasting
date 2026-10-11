# Pollen Concentration Modeling and Forecasting in Serbia Using Geostatistics and Machine Learning

## Methods

**Imputation of missing values**
- Spatio-temporal kriging (Box-Cox or log(1 + x/30) transform)
- Standardized spatio-temporal kriging
- Standardized spatio-temporal kriging with exogenous variables (temperature, humidity)
- Naive temporal nearest neighbor
- Inverse distance weighting (IDW) with temporal fallback

**Forecasting**
- SARIMAX
- Prophet
- Random Forest regression
- Naive forecasting (baseline)

**Evaluation**
- RMSLE, MAE, RMSE
- Classification into low / medium / high concentration levels
- Prediction of the high-concentration season onset date

---

## Repository Structure

The report describes the code and data in the repository root:

```text
00_era5_download.ipynb               # Meteorological data download
00_graphs.ipynb                      # Figures for the report
01_data_loading_and_exploration.ipynb
02_kriging_model.ipynb               # Imputation model comparison
02_kriging_pipeline.ipynb            # Imputation pipeline
03_1_sarimax_model.ipynb
03_2_prophet_model.ipynb
03_3_random_forest_model.ipynb
03_forecast_pipeline.ipynb           # Forecasting pipeline
data/                                # Pollen data and imputation results
meteo/                               # Meteorological data per station
models/                              # Saved models
latex-prezentacija/                  # Presentation slides
```

---

## Results

### Imputation (ragweed, 5-fold cross-validation, mean RMSLE, lower is better)

| Method | RMSLE |
|--------|-------|
| **Standardized ST Kriging (Box-Cox)** | **0.299** |
| Standardized ST Kriging + exogenous (Box-Cox) | 0.302 |
| ST Kriging (Box-Cox) | 0.307 |
| Naive Temporal Nearest | 0.358 |
| IDW with temporal fallback | 1.151 |

### Forecasting (ragweed, Požarevac, with meteorological data, RMSLE, lower is better)

| Model | 1 day | 3 days | 7 days | 10 days |
|-------|-------|--------|--------|---------|
| SARIMAX | **0.42** | **0.44** | **0.46** | **0.46** |
| Prophet | 0.46 | 0.50 | 0.59 | 0.64 |
| Random Forest | 0.43 | 0.48 | 0.55 | 0.56 |
| Naive Forecasting | 0.54 | 0.62 | 0.78 | 0.87 |

### Key findings

- Standardized spatio-temporal kriging gives the lowest imputation error for all analyzed allergens, and adding temperature and humidity changes the result very little.
- Random Forest is the thesis's recommendation for next-day forecasts when meteorological data is available.
- SARIMAX is the most stable for multi-day horizons.
- Prophet is the fastest to train and the least sensitive to meteorological inputs.
- Meteorological variables help most at short horizons, while seasonality dominates longer ones.

---

## Data

- **Pollen:** daily concentrations (grains/m³) from the [Serbian Open Data Portal](https://data.gov.rs/sr/datasets/polen-objedinjeni-podatsi-od-2016-godine/), dataset "Pollen – consolidated data since 2016", covering multiple stations across Serbia.
- **Meteorology:** per-station meteorological series (see `00_era5_download.ipynb` and `meteo/`).
- **Focus:** ragweed (*Ambrosia*) and alder, in Požarevac, Kragujevac and Pančevo.
- **Thresholds:** 30 grains/m³ (ragweed) or 60 grains/m³ (other allergens) as the lower limit, and 100 grains/m³ as the "very high" level.
