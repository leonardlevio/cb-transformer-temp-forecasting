# Predictive Maintenance: CB (Circuit Breaker) Incoming Transformer Temperature Forecasting & Early Warning System

## Project Overview
This project develops a machine learning-based forecasting system to predict the temperature of the **Circuit Breaker (CB) on the incoming side of a power transformer** using historical sensor data. The goal is to provide an early warning system that flags rising temperature trends before they reach critical or maintenance-required thresholds, helping prevent unplanned downtime and equipment damage.

> **Note**: The transformer identity has been anonymized in this public portfolio version for confidentiality.

## Business Problem
Circuit breakers on incoming transformer feeders are critical protection components. Overheating at CB contact/terminal points can indicate loose connections, contact degradation, or overloading — all of which can lead to insulation failure, tripping, or unplanned outages. By continuously monitoring and forecasting temperature trends across six sensor points (R/S/T phases, top and bottom terminals of the CB), maintenance teams can act proactively instead of reactively.

## Dataset
- **Source**: SCADA/MCGS sensor logs for the incoming CB of a power transformer (anonymized)

>**Note**: Temperature sensor data was obtained from a transformer unit belonging to PT PLN (Persero). Location details, asset IDs, and specific unit identities have been anonymized to maintain the confidentiality of the company's operational data. This project was created for learning and personal portfolio purposes and does not constitute an official representation of PLN.

- **Time column**: `MCGS_TIME` (timestamp, ~5-minute intervals)
- **Sensor columns**:
  - `R ATAS INC CB`, `S ATAS INC CB`, `T ATAS INC CB` (top terminal, phases R/S/T)
  - `R BAWAH INC CB`, `S BAWAH INC CB`, `T BAWAH INC CB` (bottom terminal, phases R/S/T)
- **Format**: Excel (`.xlsx`)

## Methodology

### 1. Data Preprocessing & EDA
- Parsed timestamps and resampled/cleaned sensor readings
- Explored distributions, daily/weekly trends, and correlation between sensor points

### 2. Feature Engineering
- **Lag features**: 24h, 48h, and 7-day lagged temperature values
- **Rolling statistics**: 24h and 7-day rolling mean and standard deviation
- **Cyclic time features**: sine/cosine encoding for hour of day, day of week, and day of year (to capture seasonality without discontinuities)

### 3. Modeling
- **Algorithm**: `HistGradientBoostingRegressor` (scikit-learn)
- **Train/test split**: chronological split (time-series aware, no shuffling) to avoid data leakage
- **Forecast horizon**: extended forecast up to 1 year ahead

### 4. Evaluation Metrics
| Metric | Value |
|--------|-------|
| MAE    | 0.575 °C |
| RMSE   | 0.717 °C |
| R²     | 0.904 |

### 5. Early Warning System
Business logic thresholds applied to forecasted temperatures to classify operational status:

| Threshold | Status |
|-----------|--------|
| < 60 °C   | Normal |
| ≥ 60 °C   | Monitoring |
| ≥ 65 °C   | Warning |
| ≥ 68 °C   | Critical |
| ≥ 70 °C   | Maintenance Required |

## Results & Insights
- The model achieves strong predictive accuracy (R² = 0.904) on unseen future data.
- Forecasts identify periods of elevated risk on the CB, enabling maintenance teams to schedule inspections proactively.
- Output forecasts and status classifications are exported for downstream reporting.

## Repository Structure
```
cb-transformer-temp-forecasting/
├── data/
│   └── sensorsuhu.xlsx
├── notebooks/
│   └── suhucb.ipynb
├── outputs/
│   └── (forecast charts, exported results)
├── README.md
└── requirements.txt
```

## How to Run
1. Clone this repository
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Open and run `notebooks/suhucb.ipynb` in Jupyter

## Tech Stack
- Python
- pandas, numpy
- scikit-learn (`HistGradientBoostingRegressor`)
- matplotlib / seaborn (visualization)
- openpyxl (Excel I/O)

## Future Improvements
- Deploy as an interactive dashboard (e.g., Streamlit) for real-time monitoring
- Incorporate additional external variables (ambient temperature, load current)
- Automate alerting via email/notification when Critical/Maintenance thresholds are forecasted

## Data Privacy Note
Transformer identity and location have been anonymized for this portfolio to protect confidential asset information. The methodology and results remain fully representative of the original analysis.

## Author
Leonard Bagaskara Cahyo Widodo - Data Science / Predictive Maintenance Project
