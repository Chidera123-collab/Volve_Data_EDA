# 🛢️ Volve Field — Exploratory Data Analysis

An end-to-end EDA of the **Volve Production Dataset**, covering oil, gas, and water production from a Norwegian North Sea field. The analysis moves from raw data cleaning through production profiling, time-series trend analysis, operational diagnostics, and correlation/feature analysis — all in Python.

---

## 📌 Project Overview

The Volve dataset contains daily production and injection records for multiple well bores, including volumes, pressures, temperatures, choke sizes, and on-stream hours. This notebook:

1. **Cleans and prepares** the raw dataset — parsing dates, separating production from injection flows, and detecting anomalies.
2. **Profiles the dataset** — identifying wells, date ranges, and total production per well.
3. **Analyses production and injection** — volumes by well, top performers, and water injection patterns.
4. **Explores time-series trends** — monthly production curves, oil decline analysis, and a 6-month moving average.
5. **Investigates operational and reservoir behaviour** — pressure trends, operating hours, and downtime by well.
6. **Computes correlations** — a heatmap of key numerical variables and identification of the strongest correlated pairs.

---

## 🗂️ Dataset

| Attribute        | Detail                                         |
| ---------------- | ----------------------------------------------- |
| **Field**        | Volve, Norwegian North Sea                      |
| **Source**       | NDI Python Class GitHub (CSV via URL)           |
| **Flow Types**   | `production`, `injection`                       |
| **Key Columns**  | `DATEPRD`, `NPD_WELL_BORE_NAME`, `FLOW_KIND`, `BORE_OIL_VOL`, `BORE_GAS_VOL`, `BORE_WAT_VOL`, `BORE_WI_VOL`, `ON_STREAM_HRS`, `AVG_DOWNHOLE_PRESSURE`, `AVG_DOWNHOLE_TEMPERATURE`, `AVG_CHOKE_SIZE_P` |

---

## 🔧 Part 1 — Data Cleaning

The raw dataset was loaded directly from a hosted CSV URL. Key cleaning steps:

- **Date parsing:** `DATEPRD` converted to `datetime` for time-series operations.
- **Duplicate check:** Verified and removed duplicate rows.
- **Flow separation:** Data split into two sub-DataFrames — `production_data` and `injection_data` — based on the `FLOW_KIND` column.
- **Null audit:** Missing values checked in `BORE_OIL_VOL`, `BORE_GAS_VOL`, `BORE_WAT_VOL` (production), and `BORE_WI_VOL` (injection).
- **Anomaly detection:** `ON_STREAM_HRS > 24` flagged as physically impossible daily values.
- **Outlier identification:** Boxplot of `BORE_OIL_VOL` used to detect high-flush early production outliers vs. stable baseline. IQR computed (Q3 − Q1) for quantitative outlier bounding.

---

## 🔍 Part 2 — Data Understanding

Key structural facts established from the dataset:

- **Number of unique wells** counted via `nunique()` on `NPD_WELL_BORE_NAME`.
- **Production vs. injection wells** enumerated and listed separately.
- **Date range** of the dataset identified (`min_date` to `max_date`).
- **Total oil produced per well** aggregated with `groupby` + `sum`.

---

## 📊 Part 3 — Production & Injection Analysis

### Total Fluid Production by Well

A grouped bar chart plots cumulative oil, gas, and water volumes for every well bore — giving an at-a-glance comparison of each well's fluid contribution and mix.

### Total Water Injected by Well

A separate bar chart shows how much water was injected into each injection well over the dataset period.

### Top 5 Oil-Producing Wells

Wells were ranked by total `BORE_OIL_VOL` and the top 5 displayed as a horizontal bar chart, making it easy to identify the highest-value assets in the field.

---

## 📈 Part 4 — Time-Series Analysis

### Monthly Production Trends

A multi-line time-series chart shows the trajectory of oil, gas, and water production across the full dataset period — surfacing seasonal patterns, ramp-up phases, and the natural production decline.

### Oil Production Decline Curve

Monthly oil volumes are plotted with a **6-month rolling moving average** overlay to smooth out short-term noise and make the underlying decline trend clearly visible — a standard diagnostic in reservoir engineering.

---

## ⚙️ Part 5 — Operational & Reservoir Analysis

### Downhole Pressure Trend

Pressure over time was plotted for the top well (`15/9-F-12`), using `AVG_DOWNHOLE_PRESSURE` as the primary signal. Declining pressure is a key indicator of reservoir depletion and informs decisions about secondary recovery.

### Monthly Average Operating Hours

Average `ON_STREAM_HRS` aggregated monthly to show how consistently the production fleet was running — dips signal field-wide downtime events.

### Operating Hours vs. Oil Production (Scatter)

A scatter plot of `ON_STREAM_HRS` against `BORE_OIL_VOL` explores how much of the production variability is explained by well uptime. Observations include:

- A general **positive correlation** — longer uptime leads to more production.
- Significant **spread at the same operating hour count**, pointing to the influence of other factors (choke size, reservoir pressure, well conditions).
- **Outliers** — high-hour wells with unusually low production flagged for further investigation.

### Downtime Days by Well

Production days with `ON_STREAM_HRS == 0` were counted per well and plotted as a red bar chart. Wells with the most downtime days are immediately visible, providing a starting point for maintenance and integrity reviews.

---

## 🔥 Part 6 — Correlation & Feature Analysis

### Correlation Heatmap

A heatmap of eight numerical variables (`AVG_DOWNHOLE_PRESSURE`, `AVG_DOWNHOLE_TEMPERATURE`, `AVG_CHOKE_SIZE_P`, `ON_STREAM_HRS`, `BORE_OIL_VOL`, `BORE_GAS_VOL`, `BORE_WAT_VOL`, `BORE_WI_VOL`) was computed using Pearson correlation and visualised with a `coolwarm` palette.

### Key Correlated Pairs

| Variable Pair                                           | Correlation | Interpretation                                                              |
| ------------------------------------------------------- | ----------- | --------------------------------------------------------------------------- |
| `BORE_GAS_VOL` ↔ `BORE_OIL_VOL`                        | **0.999**   | Near-perfect positive correlation — oil and gas move together in this reservoir |
| `AVG_DOWNHOLE_TEMPERATURE` ↔ `AVG_DOWNHOLE_PRESSURE`   | **0.968**   | Pressure and temperature strongly co-vary — typical of reservoir thermodynamics |
| `BORE_WAT_VOL` ↔ `AVG_CHOKE_SIZE_P`                    | **0.760**   | Larger choke openings associated with higher water production               |
| `ON_STREAM_HRS` ↔ `AVG_CHOKE_SIZE_P`                   | **0.531**   | Wells operating longer tend to use larger choke sizes                       |
| `ON_STREAM_HRS` ↔ `BORE_WAT_VOL`                       | **0.405**   | More uptime leads to more water produced, as expected                       |

---

## 🛠️ Requirements

- Python 3
- [pandas](https://pypi.org/project/pandas/)
- [numpy](https://pypi.org/project/numpy/)
- [matplotlib](https://pypi.org/project/matplotlib/)
- [seaborn](https://pypi.org/project/seaborn/)
- [scipy](https://pypi.org/project/scipy/)

Install dependencies:

```bash
pip install pandas numpy matplotlib seaborn scipy
```

---

## 🚀 How to Run

1. Clone the repository:
   ```bash
   git clone https://github.com/Chidera123-collab/Volve_Data_EDA.git
   cd Volve_Data_EDA
   ```
2. Open `Volve_Data_EDA.ipynb` in Jupyter Notebook, JupyterLab, or Google Colab (an "Open in Colab" badge is included at the top of the notebook).
3. Run all cells in order. The dataset is fetched directly from its hosted URL — no local file download needed.

---

## 📁 Repository Structure

```
Volve_Data_EDA/
├── Volve_Data_EDA.ipynb   # Main analysis notebook
└── README.md
```

