# Weather Trend Forecasting

## PM Accelerator Tech Assessment

> **PM Accelerator Mission:** PM Accelerator is committed to breaking down financial barriers and
> promoting educational fairness. Through its educational initiatives,
> including PMA Kids, it aims to empower underserved students, improve
> career opportunities, and foster greater diversity in the technology
> industry.

This project analyses the **Global Weather Repository** dataset and builds a leakage-safe forecasting workflow for short-term global temperature trends. It demonstrates data cleaning, exploratory data analysis, anomaly detection, environmental and spatial analysis, feature engineering, statistical forecasting, machine-learning forecasting, ensemble modelling, and model evaluation.

The project was completed for the Product Manager Accelerator technical assessment.

---

## Project Overview

The Global Weather Repository contains daily weather and air-quality observations for cities around the world. The original data includes more than 40 features covering:

- Temperature and feels-like temperature
- Precipitation and humidity
- Atmospheric pressure
- Wind speed and gust speed
- Cloud cover, visibility, and UV index
- Geographic coordinates
- Air-quality measurements, including PM2.5, PM10, carbon monoxide, ozone, nitrogen dioxide, and sulphur dioxide

The analysis covers **16 May 2024 to 18 July 2026**. Because this is approximately 2.2 years of data, the project focuses on short-term weather behaviour, seasonality, geographic variation, and forecasting rather than making long-term climate-change claims.

---

## Objectives

The project addresses both the basic and advanced assessment requirements:

1. Clean and validate the raw weather dataset.
2. Explore weather distributions, correlations, seasonality, and geographic patterns.
3. Visualise temperature and precipitation behaviour.
4. Detect invalid measurements and unusual multivariate weather observations.
5. Analyse relationships between weather and air quality.
6. Compare multiple forecasting models using chronological evaluation.
7. Create an ensemble forecast and compare it with strong baselines.
8. Assess which lagged, rolling, and seasonal features contribute most to predictions.
9. Produce a recursive 30-day forecast of the country-balanced global temperature trend.

---

## Repository Structure

A clean final repository can use the following structure:

```text
weather-trend-forecasting/
│
├── 01_Data_Preprocessing.ipynb
├── 02_Exploratory_Data_Analysis.ipynb
├── 03_Forecasting.ipynb
│
├── GlobalWeatherRepository.csv      # Download separately from Kaggle
├── weather_cleaned.csv              # Created by the preprocessing notebook
│
├── Weather_Trend_Forecasting_Report.docx
│
├── README.md
└── requirements.txt
```

The code currently expects `GlobalWeatherRepository.csv` and the generated `weather_cleaned.csv` to be accessible from the project folder.

---

## Dataset

**Dataset:** Global Weather Repository  
**Source:** [Kaggle — Global Weather Repository](https://www.kaggle.com/datasets/nelgiriyewithana/global-weather-repository/code)

### Download Instructions

1. Open the Kaggle dataset page.
2. Download the dataset archive.
3. Extract the CSV file.
4. Rename it to `GlobalWeatherRepository.csv` if necessary.
5. Place it in the root of the project folder, alongside the notebooks.

---

## Methodology

### 1. Data Cleaning and Preprocessing

The preprocessing notebook performs the following steps:

- Converts `last_updated` to a datetime field and uses it as the main time variable.
- Checks dimensions, date coverage, duplicate rows, missing values, and location coverage.
- Removes country-location pairs with fewer than 30 observations.
- Removes redundant imperial-unit fields while retaining metric equivalents.
- Identifies placeholder values such as `-9999`.
- Detects impossible values, including extreme wind speeds, pressure readings, temperatures, and negative pollutant concentrations.
- Replaces only invalid individual measurements with missing values rather than deleting otherwise useful rows.
- Creates calendar features for temporal analysis.
- Exports the cleaned data as `weather_cleaned.csv`.

### Cleaning Summary

| Measure                                  |  Result |
| ---------------------------------------- | ------: |
| Original observations                    | 153,971 |
| Original features                        |      41 |
| Final observations                       | 153,586 |
| Final features                           |      38 |
| Final country-location pairs             |     219 |
| Rows removed through location filtering  |     385 |
| Invalid individual measurements replaced |      10 |

This selective cleaning approach preserves genuine extreme weather observations while removing values that are physically impossible or clearly encoded as placeholders.

### 2. Exploratory Data Analysis

The EDA notebook examines:

- Descriptive statistics and distribution shapes
- Temperature and precipitation patterns
- Correlation structure among weather variables
- Monthly coverage and seasonal patterns
- Weather-condition categories
- Daylight duration
- Air-quality distributions and correlations
- Seasonal pollution behaviour
- Latitude-temperature relationships
- Country and location comparisons
- Interactive geographic maps
- Intraday weather and pollution patterns based on recorded update hour

### 3. Applied and Environmental Analyses

The project also includes:

- Air-quality relationships with weather variables
- Country-level PM2.5 comparisons
- EPA air-quality category analysis
- An illustrative weather-based liveability index
- A best-time-to-visit scoring framework
- A wind-resource screening proxy
- UV exposure comparisons
- Geographic temperature patterns

These analyses are descriptive or screening-oriented. They should not be interpreted as causal studies or complete national rankings because geographic and temporal coverage varies across the dataset.

### 4. Anomaly Detection

Two complementary methods are used:

- **Interquartile Range:** identifies unusual values in individual variables.
- **Isolation Forest:** identifies unusual combinations of temperature, humidity, pressure, wind speed, precipitation, and UV index.

The Isolation Forest uses standardized inputs and a 2% contamination setting, flagging **3,072 observations** for review. These rows are not automatically deleted because rare observations may represent genuine storms, heat events, or other extreme conditions.

### 5. Forecast Target Construction

The forecasting target is a **country-balanced global daily mean temperature series**.

The series is created by:

1. Averaging temperature within each country for each date.
2. Averaging the country-level values across represented countries.
3. Requiring at least 150 countries on a date.
4. Restoring the complete daily calendar.
5. Filling six missing dates using time-based interpolation.

This prevents countries with multiple sampled locations from receiving disproportionate weight.

The final forecasting series contains **794 daily observations** from 16 May 2024 to 18 July 2026.

### 6. Leakage-Safe Evaluation

The data is split chronologically:

| Split      | Dates                     | Observations | Purpose                      |
| ---------- | ------------------------- | -----------: | ---------------------------- |
| Training   | 16 May 2024–20 March 2026 |          674 | Model fitting                |
| Validation | 21 March 2026–19 May 2026 |           60 | Model and ensemble selection |
| Test       | 20 May 2026–18 July 2026  |           60 | Final evaluation             |

All test predictions use a rolling one-day-ahead process. Each prediction is generated using only information available before the forecast date. Current-day weather variables are excluded because they would not be known at prediction time.

### 7. Forecasting Models

The following models are compared:

- Rolling naive baseline
- Seven-day seasonal naive baseline
- Holt damped-trend model
- SARIMA
- Ridge regression
- Random Forest regression
- Validation-weighted Ridge and rolling-naive ensemble

Machine-learning features include lagged temperatures, rolling averages, rolling standard deviations, rolling ranges, annual sine/cosine features, and a time trend.

The final ensemble uses:

- **85% Ridge regression**
- **15% rolling naive forecast**

The weights are selected on the validation period, not the test period.

---

## Key Results

### Exploratory Findings

- Mean temperature was approximately **21.4°C**, while the median was approximately **23.7°C**.
- Precipitation and pollutant measurements were strongly right-skewed.
- Temperature and feels-like temperature had a correlation of approximately **0.980**.
- Wind speed and gust speed had a correlation of approximately **0.932**.
- Humidity was positively associated with cloud cover and negatively associated with UV index.
- August was the warmest sampled month at approximately **25.7°C**; January was the coolest at approximately **16.1°C**.
- Absolute latitude and temperature had a correlation of approximately **-0.558**, showing a clear equator-to-pole temperature gradient.
- Relationships among pollutants were generally stronger than relationships between pollutants and individual weather measurements.

### Forecast Performance

| Model                      |   MAE (°C) |  RMSE (°C) |   MAPE (%) |  sMAPE (%) |
| -------------------------- | ---------: | ---------: | ---------: | ---------: |
| **Ridge + Naive Ensemble** | **0.2206** | **0.2918** | **0.9665** | **0.9639** |
| SARIMA                     |     0.2233 |     0.2991 |     0.9760 |     0.9765 |
| Holt Damped                |     0.2262 |     0.2990 |     0.9895 |     0.9887 |
| Ridge Regression           |     0.2276 |     0.2952 |     0.9980 |     0.9948 |
| Rolling Naive              |     0.2293 |     0.3008 |     1.0027 |     1.0027 |
| Random Forest              |     0.2774 |     0.3552 |     1.2166 |     1.2182 |
| Seasonal Naive             |     0.6190 |     0.7612 |     2.7028 |     2.7333 |

The Ridge-naive ensemble achieved the best result across all four reported test metrics. Its MAE was approximately **3.8% lower** than the rolling-naive baseline.

The improvement was modest because the previous day's temperature was already a very strong predictor. This is also why the rolling-naive baseline performed competitively.

Random Forest did not outperform the simpler statistical and regularized linear approaches. The result demonstrates that increased model complexity does not automatically improve accuracy, particularly for a smooth and highly persistent aggregated series.

### Feature Importance

The previous day's temperature was the most influential feature in both Ridge regression and Random Forest. Recent lags and rolling averages also contributed strongly, while calendar features captured broader seasonality.

### Future Forecast

After evaluation, Ridge regression is retrained on the full available daily series and used to produce a recursive 30-day forecast beginning on **19 July 2026**.

The first predicted value is approximately **23.68°C**, followed by a gradual decline through late July. This forecast describes the sampled country-balanced global trend and is not a city-level weather forecast.

---

## Assessment Requirement Coverage

| Assessment requirement      | Project implementation                                              |
| --------------------------- | ------------------------------------------------------------------- |
| Handle missing values       | Analysis-specific missing-value treatment and time interpolation    |
| Handle outliers             | Physical validity checks, IQR analysis, and Isolation Forest        |
| Normalize data              | StandardScaler in Ridge and anomaly-detection pipelines             |
| Basic EDA                   | Statistics, distributions, correlations, and temporal patterns      |
| Temperature visualisation   | Distribution, monthly trend, geographic, and forecast charts        |
| Precipitation visualisation | Distribution and seasonal/weather-condition analyses                |
| Use `last_updated`          | Main datetime index for analysis and forecasting                    |
| Basic forecasting model     | Naive and Holt baselines                                            |
| Multiple forecasting models | Naive, Holt, SARIMA, Ridge, Random Forest, and ensemble             |
| Ensemble model              | Validation-weighted Ridge and rolling-naive ensemble                |
| Anomaly detection           | IQR and Isolation Forest                                            |
| Climate analysis            | Short-term seasonal and regional variation, with limitations stated |
| Environmental impact        | Air-quality distributions and weather-pollution relationships       |
| Feature importance          | Ridge coefficients and Random Forest feature importance             |
| Spatial analysis            | Latitude analysis, rankings, and maps                               |
| Geographic patterns         | Country and sampled-location comparisons                            |
| Model evaluation            | MAE, RMSE, MAPE, and sMAPE                                          |
| PM Accelerator mission      | Included in notebook and report documentation                       |

---

## Installation

### Prerequisites

- Python **3.10, 3.11, or 3.12** is recommended.
- Git
- A Kaggle account to download the dataset

### 1. Clone the Repository

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
cd weather-trend-forecasting
```

### 2. Create a Virtual Environment

#### Windows

```bash
python -m venv .venv
.venv\Scripts\activate
```

#### macOS or Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Start Jupyter

```bash
jupyter lab
```

You can also use:

```bash
jupyter notebook
```

---

## Running the Project

Run the notebooks in this order:

1. `01_Data_Preprocessing.ipynb`
2. `02_Exploratory_Data_Analysis.ipynb`
3. `03_Forecasting.ipynb`

The first notebook reads `GlobalWeatherRepository.csv` and creates `weather_cleaned.csv`. The EDA and forecasting notebooks then load the cleaned dataset.

For reproducible results, restart the kernel and run all cells from top to bottom in each notebook before moving to the next notebook.

---

## Creating or Updating `requirements.txt`

This repository includes a manually curated `requirements.txt` containing the libraries directly needed by the notebooks. This is generally cleaner than exporting every package installed in a development environment.

To install it:

```bash
pip install -r requirements.txt
```

To capture every exact package version in the current environment, use:

```bash
pip freeze > requirements-lock.txt
```

A full `pip freeze` file may contain unrelated packages, so it is better used as an optional lock or environment snapshot rather than as the main human-maintained requirements file.

After adding a new library to the project, add the package to `requirements.txt`, reinstall the file in a fresh environment, and rerun all notebooks to verify that the project remains reproducible.

---

## Limitations

- The dataset covers approximately 2.2 years, which is insufficient for strong long-term climate-change conclusions.
- Countries and locations have uneven observation coverage.
- Country rankings reflect the sampled locations and dates rather than complete national averages.
- The forecasting target is an aggregated country-balanced global series, not a local weather forecast.
- Recursive forecasting can accumulate error as predicted values are reused to create later features.
- The displayed uncertainty range is heuristic rather than a calibrated probabilistic interval.
- Correlations and feature importance indicate association or predictive usefulness, not causation.
- The Isolation Forest contamination setting controls the proportion flagged and does not establish that all flagged observations are errors.

---

## Recommended Future Improvements

- Extend the time range to support more reliable climate and seasonal analysis.
- Build separate city-level or regional forecasting models.
- Add external variables such as elevation, land use, population density, and emissions data.
- Use time-series cross-validation across multiple rolling windows.
- Create calibrated probabilistic forecast intervals.
- Compare gradient boosting and dedicated forecasting libraries.
- Package the workflow into reusable Python modules or an automated pipeline.
- Add unit tests for preprocessing and feature-generation functions.

---

## Demo

- **Demo video:** (https://drive.google.com/file/d/1kWKAECqZJMHtslw282Hx-wFhXZQBgfTH/view?usp=sharing)

---

## Author

**Name:** `Aeiman Imtiaz`
**Assessment:** Product Manager Accelerator — Weather Trend Forecasting

---
