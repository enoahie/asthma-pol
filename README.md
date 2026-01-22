# US Asthma Risk “What‑If” Dashboard

An interactive, ML‑powered dashboard that turns county‑level air quality into actionable asthma risk insights. It predicts Low/Medium/High risk from CACES pollutants and CDC PLACES data, and lets users simulate 10–50% pollutant/smoking reductions with instant map updates—making policy trade‑offs easy to see.

## Live Demo
- https://enoahie.github.io/asthma-pol/

## Run Locally (Preview)
1) From repo root:
```bash
python3 -m http.server 8000
```
2) Open http://localhost:8000/ (redirects to `src/visualization.html`)
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

# INSTALLATION

### PREREQUISITES
- Python 3.x and the following libraries libraries:
  - pandas
  - geopandas
  - numpy
  - matplotlib
  - scikit-learn
  - imblearn
- A modern web browser (Chrome, Firefox, Safari, Edge)

### DATA SOURCES

1. CACES LUR Data for 2017-2019
   1. Navigate to the [CACES Data Download Page](https://www.caces.us/data).
   2. Enter your email and organization.
   3. Select "LUR", "National", "County", "O3", "SO2", "NO2", "PM2.5", "2017-2019" and click "GET DATA".
   4. You should receive an email with a data download shortly. Make sure to check your junk folder.
2. PLACES County Data from 2020 & 2021 Releases
   1. 2020 Data: Navigate to [PLACES: County Data (GIS Friendly Format), 2020 release](https://data.cdc.gov/500-Cities-Places/PLACES-County-Data-GIS-Friendly-Format-2020-releas/mssc-ksj7/about_data), click "Export", make sure "CSV" is selected for "Export format", click "Download".
   2. 2021 Data: Navigate to [PLACES: County Data (GIS Friendly Format), 2021 release](https://data.cdc.gov/500-Cities-Places/PLACES-County-Data-GIS-Friendly-Format-2021-releas/kmvs-jkvx/about_data), click "Export", make sure "CSV" is selected for "Export format", click "Download".
3. 2010 US County Shapefile
   1. Navigate to [this US Census Bureau link](https://www2.census.gov/geo/tiger/TIGER2010/COUNTY/2010/tl_2010_us_county10.zip) and the download will start.
4. 2010 US County GeoJSON
   1. Navigate to [this site](https://eric.clst.org/tech/usgeojson/), click on "GeoJSON" for the "US Counties" and "20m" cell.

### DATA PROCESSING

If you would like to process the raw data, follow the below steps:

1. Go to `asthma-pol/src/data/` and place all of the files you downloaded from the previous section. Make sure to extract the files from `tl_2010_us_county10.zip`.
2. Go back to to `asthma-pol/src/` and run all of the cells in `build_dataset_rf.ipynb`. This will create all of the data that the visualization needs.

# EXECUTION / QUICK START

1. Navigate to `/asthma-pol/` and run `start_server.bat` (Windows) or `python3 -m http.server 8000`.
2. Open http://localhost:8000/ → redirects to the visualization.
3. Use “Target Variable”, “Filter: Asthma Percentile”, and “What If Reduction (%)”.

## How It Works (High Level)
- Data: CACES pollutant models (PM2.5, NO2, O3, SO2), CDC PLACES (asthma, smoking), county GeoJSON; joined by FIPS.
- ML: 3‑class classification of asthma prevalence (tertiles). scikit‑learn model sweep with 5‑fold CV; VotingClassifier (RF + KNN + GB) ≈ 0.68 accuracy/F1; dummy baseline ≈ 0.34.
- Scenarios: Offline generation of reduced‑feature predictions (0.5–0.9 multipliers) saved as CSVs (`src/data/what-if/*.csv`), each with `prediction` (0/1/2).
- Frontend: Static HTML/CSS + D3 v5; TopoJSON map; CSVs loaded client‑side; county merge by FIPS; choropleth + distribution plot; percentile filter and tooltips.

## Deploy to GitHub Pages
1) Push this repo to GitHub.
2) In GitHub → Settings → Pages:
   - Source: Deploy from a branch
   - Branch: `main` (or `master`), Folder: `/ (root)`
3) Open `https://<your-username>.github.io/<repo>/` (root `index.html` redirects to `src/visualization.html`).
4) Optional: add the live link above in “Live Demo”.