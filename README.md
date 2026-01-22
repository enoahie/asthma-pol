# US Asthma Risk “What‑If” Dashboard

An interactive, ML‑powered dashboard that turns county‑level air quality into actionable asthma risk insights. It predicts Low/Medium/High risk from CACES pollutants and CDC PLACES data, and lets users simulate 10–50% pollutant/smoking reductions with instant map updates—making policy trade‑offs easy to see.

## Live Demo
- https://enoahie.github.io/asthma-pol/

## Run Locally
1) From repo root:
```bash
python3 -m http.server 8000
```
2) Open http://localhost:8000/
3) Use the controls:
- Target Variable: select PM2.5, NO2, O3, SO2, Smoking, or Index.
- Filter: Asthma Percentile: show counties at or above selected prevalence.
- “What If” Reduction (%): apply 10–50% reductions (loads precomputed scenario predictions).
- Hover: see county/state and risk category. “Counties shown” updates live.

# DESCRIPTION

This project is a data visualization tool designed to explore the
relationship between environmental quality and public health. It provides an 
interactive dashboard for analyzing how major air pollutants (PM2.5, NO2, O3, SO2)
and smoking prevalence correlate with asthma risk across the United States.

It features a dynamic "What-If" scenario engine that allows users to simulate the 
impact of reducing specific pollutants (by 10% to 50%) on asthma risk, visualizing 
potential health outcomes based on predictive model data.

## How It Works (High Level)
- It brings together three pieces of information for every county: air pollution levels, health estimates about asthma and smoking, and the county boundaries.
- A trained model classifies each county as Low, Medium, or High asthma risk based on those inputs.
- The app includes precomputed “what‑if” versions of the data that answer: “What would risk look like if this pollutant (or smoking) were reduced by 10–50%?”
- The website shows a color‑coded map and a simple chart. Use the dropdown and sliders to explore; the view updates instantly.

## How It Works (Low Level)
- When the page opens, it loads the county shapes and the main data file and draws the map and chart.
- The “Target Variable” dropdown chooses which factor the side chart displays and which “what‑if” files the slider will use.
- The “Filter: Asthma Percentile” slider focuses the map on counties with higher measured asthma prevalence so patterns are easier to see.
- The “What‑If Reduction (%)” slider looks up a pre‑made file (for the selected variable and reduction level) and updates each county’s risk category on the map and in the chart.
- Counties shown in gray are either filtered out by your settings or don’t have data for the selected view.
- If a matching “what‑if” file isn’t available, the site falls back to the original data.
- Data files live in `src/data/` (main file) and `src/data/what-if/` (scenario files). The county shapes file is also in `src/data/`.

## Data Preparation and Modeling
- Data preparation
  - Sources: CACES pollutant models (PM2.5, NO2, O3, SO2), CDC PLACES (asthma, smoking), and 2010 county boundaries (GeoJSON).
  - Join: counties matched via FIPS codes; numbers cleaned/standardized; Alaska/Hawaii excluded where coverage was limited.
  - Features: pollutant indicators (e.g., `pred_wght_pm25/no2/o3/so2`), smoking rate (`CSMOKING_AdjPrev`), and a composite index where available.
- Labeling
  - Target: asthma prevalence (`CASTHMA_AdjPrev`) binned into tertiles → Low / Medium / High for interpretable classes.
- Training and evaluation
  - Split: 70/30 train–test; 5‑fold cross‑validation on training data; metrics: accuracy and weighted F1.
  - Models compared: RandomForest, GradientBoosting, KNeighbors, LogisticRegression, AdaBoost, MLP, DecisionTree, SGD, and a VotingClassifier (RF + KNN + GB).
  - Result: VotingClassifier ≈ 0.68 accuracy/F1; dummy (“guessing”) baseline ≈ 0.34.
- Why this model
  - Handles non‑linear tabular patterns, remains stable across regions, and outputs clear 3‑level risk categories suited to a choropleth.

## Libraries & Technologies
- Data & ML
  - Jupyter Notebook (`src/build_dataset_rf.ipynb`), pandas, numpy, scikit‑learn, imblearn
  - geopandas, matplotlib during exploration/EDA
  - Models tested: RandomForest, GradientBoosting, KNeighbors, LogisticRegression, AdaBoost, MLP, DecisionTree, SGD; selected VotingClassifier (RF + KNN + GB)
- Visualization/UI
  - HTML/CSS with D3.js v5 and TopoJSON
  - Choropleth map (GeoAlbers USA), percentile filter, distribution plot, tooltips
- Data formats
  - CSV (baseline and scenario predictions), GeoJSON (counties), TopoJSON (topology)

## Data Sources

1. CACES LUR Data for 2017-2019
2. PLACES County Data from 2020 & 2021 Releases
3. 2010 US County Shapefile
4. 2010 US County GeoJSON
