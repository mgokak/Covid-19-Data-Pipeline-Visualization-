# COVID-19 Data Pipeline - Jupyter Notebook

> Python data preprocessing pipeline for extracting, transforming, and loading COVID-19 data for Tableau visualization

---

## Overview

This Jupyter notebook (`covid_data_extract_format.ipynb`) performs automated ETL operations on COVID-19 data from Our World in Data, processing 400,000+ records to create Tableau-ready datasets with geographic coordinates.

**Key Outputs:**
- `COVID19_Tableau_Data.csv` - Clean dataset with lat/lon for mapping (~417K records)
- `COVID19_Analysis_Report.xlsx` - Multi-sheet Excel report with summary statistics

---

## What This Notebook Does

### 1. **Data Collection** (Section 2)
- Downloads 400,000+ COVID-19 records from Our World in Data API
- Uses GitHub mirror as fallback for reliability
- Handles network errors gracefully

### 2. **Geographic Enrichment** (Section 3)
- Adds latitude/longitude coordinates for 60+ countries
- Enables Tableau map visualizations
- Covers major countries representing ~90% of global cases

### 3. **Data Preprocessing** (Sections 4-5)
- Removes continental aggregates (keeps only country-level data)
- Handles missing values using forward-fill for cumulative metrics
- Filters to 32 key columns from original 67

### 4. **Feature Engineering** (Section 5)
- Calculates **Case Fatality Rate** (CFR): `(total_deaths / total_cases) × 100`
- Creates time-based features: year, month, quarter, week, day_of_week
- Normalizes metrics to per-capita rates (per million population)

### 5. **Summary Statistics** (Section 7)
- Global totals: cases, deaths, CFR
- Top 10 countries by total cases
- Continent-level aggregations
- Coverage statistics for geographic data

### 6. **Excel Report Generation** (Sections 8-9)
- 5 professionally formatted sheets:
  1. Latest Snapshot (current data per country)
  2. Recent Trends (last 90 days, top 10 countries)
  3. Summary Statistics (global KPIs)
  4. Top Countries by Cases
  5. Continent Summary
- Auto-formatted headers, column widths, frozen panes

---

## Output Files

### `COVID19_Tableau_Data.csv`
```
Columns: 32
Rows: ~417,680
Size: ~28 MB

Key Fields:
├─ date, location, iso_code, continent
├─ latitude, longitude (for mapping)
├─ total_cases, new_cases, total_deaths, new_deaths
├─ total_cases_per_million, total_deaths_per_million
├─ case_fatality_rate (calculated)
├─ population, population_density, median_age
└─ year, month, quarter, week, day_of_week
```

### `COVID19_Analysis_Report.xlsx`
```
Sheets: 5
Format: Professional formatting with color-coded headers

Sheet 1: Latest Snapshot (248 countries)
Sheet 2: Recent Trends (90-day window, top countries)
Sheet 3: Summary Statistics (global KPIs)
Sheet 4: Top Countries by Cases (ranked)
Sheet 5: Continent Summary (aggregated by region)
```

---

## How to Run

### Prerequisites
```bash
pip install pandas numpy openpyxl requests
```

---

## Data Processing Steps

### Step 1: Download (Section 2)
```python
Source: Our World in Data GitHub Mirror
URL: https://raw.githubusercontent.com/owid/covid-19-data/master/public/data/owid-covid-data.csv
Records: 429,435 raw records
Countries: 255 territories
Date Range: 2020-01-01 to 2024-08-14
```

### Step 2: Clean (Section 4)
```python
Removed:
├─ Continental aggregates (World, Europe, Asia, etc.)
├─ Income groups (High income, Low income, etc.)
└─ Supranational entities (European Union)

Retained:
└─ 248 countries with actual data
```

### Step 3: Enrich (Section 3)
```python
Added Geographic Coordinates:
├─ 63 countries with lat/lon
├─ Coverage: ~90% of global COVID cases
└─ Enables Tableau heatmap visualization
```

### Step 4: Transform (Section 5)
```python
Calculated Fields:
├─ case_fatality_rate = (total_deaths / total_cases) × 100
├─ year, month, quarter, week
└─ day_of_week (for pattern analysis)

Handled Missing Values:
└─ Forward-fill for cumulative metrics (total_cases, total_deaths)
```

### Step 5: Export (Sections 6, 8)
```python
Output 1: COVID19_Tableau_Data.csv
└─ All 417,680 processed records

Output 2: COVID19_Analysis_Report.xlsx
└─ 5 sheets with formatted summaries
```

---

## Key Technical Decisions

### Why Forward-Fill for Missing Values?
```python
# Cumulative metrics shouldn't have gaps
df['total_cases'] = df.groupby('location')['total_cases'].fillna(method='ffill')
```
Cumulative totals (cases, deaths) shouldn't decrease, so we carry forward the last known value within each country.

### Why Remove Continental Aggregates?
```
Avoids double-counting in Tableau dashboards
Example: "Asia" total would include India, China, etc. separately
```

### Why Both CSV and Excel?
```
CSV: For Tableau (handles large datasets efficiently)
Excel: For stakeholders (human-readable, formatted)
```

---

## Data Quality Notes

### Missing Data Patterns
- **Vaccination data**: Sparse before 2021
- **ICU/Hospitalization**: Only ~40 countries report
- **Testing data**: Inconsistent across regions

### Known Limitations
1. **Coordinates**: Only 63 major countries (minor nations excluded)
2. **Reporting delays**: Some countries report weekly, not daily
3. **Data revisions**: Historical data subject to corrections
4. **Definition changes**: Case definitions evolved over time

### Data Sources
- **Primary**: Our World in Data (aggregates WHO, ECDC, national sources)
- **Population**: UN World Population Prospects 2020
- **Socioeconomic**: World Bank, IMF

---

## Dependencies

```
pandas >= 1.3.0    # Data manipulation
numpy >= 1.21.0    # Numerical operations
openpyxl >= 3.0.9  # Excel file generation
requests >= 2.26.0 # HTTP requests for data download
```

---

## Author
**Manasa Vijayendra Gokak**  
Graduate Student – Data Science  
