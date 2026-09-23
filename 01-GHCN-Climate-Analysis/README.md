# Global Climate Data Analysis Using Apache Spark

## Project Overview

This project analyses large-scale historical climate data from the **Global Historical Climatology Network Daily (GHCN-Daily)** dataset using **Apache Spark and PySpark**.

The project was designed to work with climate data at a scale that requires distributed processing. The complete daily dataset contained more than **3.13 billion weather observations**, covering historical records from **1750 to 2025**.

The analysis combines large-scale data processing, data quality validation, metadata integration, geospatial analysis, time-series analysis, statistical analysis, and visualisation.

The project focused on three major areas:

1. **Processing and enriching global weather-station data**
2. **Analysing billions of historical weather observations using PySpark**
3. **Investigating New Zealand temperature trends and global precipitation patterns**

A major focus was learning how to process large datasets efficiently without attempting to load the complete dataset into local memory.

---

## Technologies Used

- Python
- Apache Spark
- PySpark
- Microsoft Azure
- Azure Blob Storage
- Spark DataFrames
- Pandas
- NumPy
- SciPy
- Matplotlib
- Plotly
- Parquet
- Jupyter Notebook
- Statistical Analysis
- Geospatial Analysis
- Time-Series Analysis

---

# Dataset

The project uses the **Global Historical Climatology Network Daily (GHCN-Daily)** dataset.

GHCN-Daily contains historical weather observations collected from weather stations around the world.

The project worked with both:

- Daily climate observations
- Weather-station metadata

The main daily weather elements analysed were:

| Element | Description |
|---|---|
| `PRCP` | Precipitation |
| `TMAX` | Maximum daily temperature |
| `TMIN` | Minimum daily temperature |
| `SNOW` | Snowfall |
| `SNWD` | Snow depth |

The station metadata contained information such as:

- Station ID
- Station name
- Country
- State
- Latitude
- Longitude
- Elevation
- First active year
- Last active year
- Available weather elements
- Climate monitoring network information

---

# Dataset Scale

The complete daily dataset contained:

- **3,139,143,397 weather observations**
- Historical observations from **1750 to 2025**
- Approximately **13 GB** of compressed daily data
- **129,657 weather stations** in the station metadata

The scale of the dataset made Apache Spark suitable for processing the data using distributed operations rather than loading the entire dataset into local memory.

---

# Project Workflow

The overall workflow was:

```text
GHCN Raw Data
      │
      ├── Daily Weather Observations
      │
      └── Station / Country / State / Inventory Metadata
      │
      ▼
PySpark Data Loading
      │
      ▼
Schema Definition & Fixed-Width Parsing
      │
      ▼
Cleaning & Metadata Integration
      │
      ▼
Enriched Station Dataset
      │
      ▼
Parquet Storage
      │
      ▼
Large-Scale Spark Analysis
      │
      ├───────────────┬─────────────────┐
      ▼               ▼                 ▼
Station Analysis   NZ Temperature   Global Precipitation
      │               │                 │
      ▼               ▼                 ▼
Geospatial        Time-Series        Data Quality
Analysis          Analysis            Analysis
      │               │                 │
      └───────────────┴─────────────────┘
                      │
                      ▼
                 Visualisation
```

---

# 1. Data Exploration

The first stage of the project involved understanding how the GHCN data was organised in Azure Blob Storage.

The dataset contained several different files and formats, including:

- Daily weather observations
- Weather-station metadata
- Country metadata
- State metadata
- Station inventory information

Before performing analysis, I investigated:

- File structure
- File sizes
- Data formats
- Number of records
- Column structures
- Data types
- Missing values
- Relationships between datasets

This helped determine the appropriate loading and processing strategy for each dataset.

---

# 2. Loading Daily Weather Data

The daily observations were processed using Apache Spark.

An explicit PySpark schema was defined for fields including:

- Station ID
- Date
- Weather element
- Observation value
- Measurement flag
- Quality flag
- Source flag
- Observation time

Using an explicit schema ensured that fields such as dates and measurements were loaded using appropriate data types.

The general process was:

```text
Daily Climate Files
        ↓
Define PySpark Schema
        ↓
Load with Spark
        ↓
Validate Data Types
        ↓
Clean / Filter
        ↓
Large-Scale Analysis
```

---

# 3. Processing Fixed-Width Metadata

Some GHCN metadata files were stored as **fixed-width text files** rather than standard CSV files.

These included information about:

- Stations
- Countries
- States
- Station inventories

The files were loaded using:

```python
spark.read.text()
```

Fields were then extracted from fixed character positions using PySpark string operations such as `substring()`.

For example, station information could be separated into:

```text
Station ID
Latitude
Longitude
Elevation
State
Station Name
Network Information
```

This converted unstructured fixed-width text into structured Spark DataFrames that could be joined with other datasets.

---

# 4. Station Metadata Integration

Multiple metadata sources were combined to create a more useful weather-station dataset.

The processing workflow included:

```text
Station Metadata
      │
      ├── Country Metadata
      ├── State Metadata
      └── Inventory Metadata
      │
      ▼
Spark Joins & Aggregations
      │
      ▼
Enriched Station Dataset
```

The enriched dataset contained information such as:

- Station ID
- Station name
- Country
- State
- Latitude
- Longitude
- Elevation
- First active year
- Last active year
- Number of weather elements
- Core weather elements
- Monitoring-network information

This provided a reusable station-level dataset for later analysis.

---

# 5. Parquet Storage

After processing and enriching the station metadata, the resulting DataFrame was saved to Azure Blob Storage using **Parquet format**.

Parquet was useful because it provides:

- Columnar storage
- Compression
- Schema preservation
- Efficient reading of selected columns
- Better suitability for analytical workloads

The workflow therefore became:

```text
Raw Metadata
      ↓
PySpark Processing
      ↓
Cleaned & Enriched Data
      ↓
Parquet
      ↓
Reuse in Later Analysis
```

---

# 6. Optimising Large-Scale Joins

One important part of the project involved checking whether station IDs appearing in the daily observations were missing from the station metadata.

A straightforward approach would have been to perform a large `LEFT JOIN`.

However, the daily dataset contained billions of records, so joining the entire dataset unnecessarily would have been computationally expensive.

Instead, I used a more efficient strategy based on:

```text
Distinct Station IDs
        +
Station Metadata
        ↓
LEFT ANTI JOIN
```

A **LEFT ANTI JOIN** returns records from the left dataset that do not have a matching record in the right dataset.

Conceptually:

```text
Daily Station IDs
        │
        ├──── Match ──── Station Metadata
        │
        ▼
Unmatched Station IDs
```

This allowed the validation to focus on station identifiers instead of joining billions of complete observation records.

The analysis confirmed that the station IDs in the complete daily dataset were represented in the station metadata.

This part of the project demonstrated an important big-data principle:

> The way an operation is designed can be just as important as the result when processing billions of records.

---

# 7. Weather Station Analysis

After preparing the station metadata, I performed exploratory analysis using PySpark DataFrame operations.

Operations included:

```python
filter()
groupBy()
agg()
join()
collect_set()
```

The analysis investigated:

- Total weather stations
- Active weather stations
- Country distribution
- State and territory distribution
- Northern and Southern Hemisphere stations
- Climate monitoring networks
- Weather elements recorded by stations

The metadata contained **129,657 weather stations**.

This stage demonstrated how Spark can be used for exploratory analysis without moving large datasets into Pandas.

---

# 8. Core Weather Element Analysis

The complete daily dataset contained:

**3,139,143,397 observations.**

The five main weather elements were counted using Spark.

| Weather Element | Number of Observations |
|---|---:|
| Precipitation (`PRCP`) | **1,079,767,077** |
| Maximum Temperature (`TMAX`) | **460,114,659** |
| Minimum Temperature (`TMIN`) | **458,928,768** |
| Snowfall (`SNOW`) | **359,249,644** |
| Snow Depth (`SNWD`) | **300,711,620** |

Precipitation was therefore the most frequently recorded of the five core weather elements.

These counts also illustrate the scale of the data being processed.

---

# 9. Data Completeness – TMAX and TMIN

A data-quality investigation was performed to determine how often a maximum temperature observation existed without a corresponding minimum temperature observation.

The comparison was performed using:

```text
Station ID + Date
```

The weather elements available for each station-date combination were grouped and compared.

The analysis identified:

- **10,660,214 TMAX observations without matching TMIN**
- **28,754 stations associated with unmatched TMAX observations**

This demonstrated that even very large datasets can contain incomplete combinations of related measurements.

It also reinforced the importance of checking data completeness before performing climate analysis.

---

# 10. Geospatial Analysis

The project also included geographic analysis of New Zealand weather stations.

Station metadata contained:

```text
Latitude
Longitude
```

which allowed geographic distances between stations to be calculated.

---

## Haversine Distance

I implemented the **Haversine formula** to calculate approximate great-circle distances between locations on the Earth.

Conceptually, the calculation used:

```text
Station A
Latitude / Longitude
       │
       ▼
Haversine Distance
       ▲
       │
Station B
Latitude / Longitude
```

The Python function was registered as a **Spark User Defined Function (UDF)** so it could be applied within Spark.

---

## Pairwise Station Comparison

New Zealand stations were compared using a Spark `CROSS JOIN`.

The workflow was:

```text
NZ Stations
     │
     ├──── CROSS JOIN ──── NZ Stations
     │
     ▼
Station Pairs
     │
     ▼
Haversine UDF
     │
     ▼
Distance Between Stations
```

This allowed station pairs to be compared geographically.

The analysis identified **Paraparaumu AWS and Wellington Aero AWS** as the closest pair in the analysed set, with a calculated distance of approximately **50.53 km**.

This part of the project demonstrated how custom geographic calculations can be integrated into a distributed Spark workflow.

---

# 11. New Zealand Temperature Analysis

The next major part of the project focused specifically on New Zealand temperature observations.

The analysis used:

- `TMIN` – minimum temperature
- `TMAX` – maximum temperature

The analysed New Zealand dataset included:

- **15 weather stations**
- Temperature observations covering **1940–2025**

The objective was to investigate long-term temperature patterns and differences between stations.

---

# 12. Preparing New Zealand Temperature Data

The full daily dataset was filtered to retain:

```text
Country = New Zealand
Element = TMIN or TMAX
```

The data was then transformed so that minimum and maximum temperatures could be analysed by:

- Station
- Date
- Year
- Month
- Season

The workflow was:

```text
3.1+ Billion Observations
        ↓
Filter NZ Stations
        ↓
Filter TMIN / TMAX
        ↓
Aggregate with Spark
        ↓
Smaller Analytical Dataset
        ↓
Convert to Pandas
        ↓
Visualise
```

An important design decision was to **aggregate the data in Spark before converting it to Pandas**.

This avoided trying to move the complete distributed dataset into local memory.

---

# 13. Station-Level Temperature Trends

Yearly average minimum and maximum temperatures were calculated for individual New Zealand weather stations.

The analysis investigated:

- Historical TMIN
- Historical TMAX
- Missing years
- Long-term trends
- Differences between stations

The visualisations made it possible to compare how temperatures changed across different locations and time periods.

---

# 14. Linear Regression Trend Analysis

Linear regression was used to estimate long-term temperature trends for each station.

The analysis used:

```python
scipy.stats.linregress()
```

Conceptually:

```text
Year → Independent Variable
Temperature → Dependent Variable
```

The slope of the regression line was used to identify whether temperature showed an increasing or decreasing trend over time.

The analysis found that:

**11 of the 15 analysed New Zealand stations showed increasing trends in both TMIN and TMAX.**

However, station records did not all cover exactly the same periods.

For this reason, differences in:

- station operating periods
- missing years
- observation coverage

were important considerations when interpreting the results.

---

# 15. Seasonal Temperature Analysis

Temperature data was also analysed by month and season.

Monthly average values were calculated to investigate New Zealand's seasonal temperature cycle.

The results showed the expected seasonal pattern:

```text
Summer
   ↑
Higher temperatures

Winter
   ↓
Lower temperatures
```

This provided another way of validating and understanding the temperature data.

---

# 16. Temperature Distribution Analysis

The project also investigated the distribution of temperature observations.

Visualisations were used to compare:

- TMIN distributions
- TMAX distributions
- Differences between stations
- National temperature patterns

These analyses helped identify:

- typical temperature ranges
- variability
- extreme values
- differences between locations

---

# 17. Global Precipitation Analysis

The second major visual analysis focused on global precipitation.

The original dataset contained:

**1,079,767,077 precipitation observations.**

The analysis investigated precipitation by:

- Year
- Country
- Observation count
- Average precipitation

Because the raw precipitation dataset itself contained more than one billion records, the data was processed and aggregated in Spark before visualisation.

---

# 18. Precipitation Data Quality

Before calculating global precipitation statistics, the GHCN quality flags were investigated.

Only observations with acceptable quality information were retained for the main analysis.

The quality-control process removed:

**629,024 precipitation observations.**

Approximately:

**1,079,138,053 observations**

remained after this filtering stage.

This demonstrated that data quality checks are essential even when working with established scientific datasets.

---

# 19. Country-Level Aggregation

The cleaned precipitation data was grouped by:

```text
Country
+
Year
```

Average daily precipitation was then calculated for each country-year combination.

The workflow was:

```text
1.07+ Billion PRCP Observations
        ↓
Quality Filtering
        ↓
Join Station / Country Information
        ↓
Group by Country + Year
        ↓
Calculate Average Precipitation
        ↓
Store Aggregated Results
        ↓
Convert Small Results to Pandas
        ↓
Visualise
```

This approach allowed a very large raw dataset to be reduced to a manageable analytical dataset.

---

# 20. Investigating Extreme Precipitation Values

Some countries appeared to have unusually high average precipitation values.

Rather than assuming that these represented genuine national climate patterns, I investigated the underlying observations.

The investigation considered:

- Number of observations
- Data coverage
- Quality flags
- Extreme measurements
- Sparse historical data

This showed an important analytical issue:

> A very high average does not necessarily mean that a country generally receives extreme rainfall.

For example, an average based on a very small number of observations may not be representative of the country's overall climate.

Therefore, precipitation values were interpreted together with observation counts and data coverage.

---

# 21. 2024 Global Precipitation Analysis

The project examined global precipitation patterns specifically for **2024**.

The analysis included:

- Country-level average precipitation
- Number of available observations
- Distribution analysis
- Descriptive statistics
- Histograms
- Box plots
- Outlier analysis
- Countries with high precipitation
- Countries with low precipitation

The 2024 analysis contained precipitation information for **179 countries**, while some countries did not have usable observations for that year.

---

# 22. Outlier Detection

The precipitation distribution was investigated for unusually high values.

The **Interquartile Range (IQR)** method was used as part of the outlier investigation.

Conceptually:

```text
Q1 = 25th percentile
Q3 = 75th percentile

IQR = Q3 - Q1
```

Potential extreme observations can then be investigated relative to the central distribution.

Importantly, values identified as statistical outliers were not automatically treated as errors.

Instead, the underlying observation counts and data quality were examined before interpreting them.

---

# 23. Preparing Geographic Data

Country names in the climate data did not always directly match the names expected by geographic visualisation libraries.

Additional cleaning was therefore required.

The process included:

```text
GHCN Country Names
        ↓
Clean Country Names
        ↓
Standardise Names
        ↓
Convert to ISO-3 Codes
        ↓
Handle Unmatched Locations
        ↓
Join with Precipitation Results
```

This created geographic identifiers suitable for global mapping.

---

# 24. Global Precipitation Choropleth

An interactive global precipitation map was created using **Plotly**.

The map used ISO-3 country codes to connect precipitation values to geographic locations.

The choropleth provided a geographic view of differences in average precipitation across countries.

This stage combined:

- Big-data processing
- Geographic data preparation
- Country-code standardisation
- Aggregation
- Interactive visualisation

---

# 25. Spark-to-Pandas Strategy

A key technical principle throughout the project was deciding **when to use Spark and when to use Pandas**.

Spark was used for:

- Billions of raw records
- Filtering
- Joins
- Grouping
- Aggregation
- Large-scale validation
- Data transformation

Pandas was used only after Spark had reduced the data to a manageable size.

The general strategy was:

```text
Very Large Dataset
        ↓
Apache Spark
        ↓
Filter / Join / Aggregate
        ↓
Small Result Dataset
        ↓
Pandas
        ↓
Matplotlib / Plotly
```

This avoided unnecessary memory problems and provided a practical workflow for combining distributed processing with Python visualisation libraries.

---

# 26. Key Findings

## Dataset Scale

The project successfully processed a daily climate dataset containing:

**3,139,143,397 observations**

covering the period:

**1750–2025**

---

## Core Weather Measurements

Precipitation was the most frequently recorded core weather element:

**1,079,767,077 PRCP observations**

followed by TMAX, TMIN, SNOW and SNWD.

---

## Data Completeness

The analysis identified:

**10,660,214 TMAX observations without corresponding TMIN observations**

across:

**28,754 stations**

demonstrating the importance of completeness checks.

---

## New Zealand Temperature

Temperature data from **15 New Zealand stations** was analysed across the available period from **1940–2025**.

Linear trend analysis found that:

**11 of 15 stations showed increasing trends in both minimum and maximum temperatures.**

These results were interpreted with consideration of different station operating periods and missing observations.

---

## Global Precipitation

More than **1.07 billion precipitation observations** were processed.

Quality filtering removed:

**629,024 observations**

before country-level precipitation analysis.

The analysis also showed that extreme country-level averages need to be interpreted carefully when observation coverage is limited.

---

# 27. Key Skills Demonstrated

This project demonstrates practical experience across several areas of data engineering and analytics.

### Big Data Processing

- Apache Spark
- PySpark
- Distributed data processing
- Spark DataFrames
- Processing billions of records
- Large-scale filtering
- Large-scale aggregation
- Join optimisation

### Cloud Data

- Microsoft Azure
- Azure Blob Storage
- Reading cloud-hosted datasets
- Writing processed datasets to cloud storage

### Data Engineering

- Explicit schema creation
- Fixed-width file parsing
- Data cleaning
- Data transformation
- Metadata integration
- Data validation
- Parquet storage
- Efficient join strategies

### Data Analysis

- Exploratory data analysis
- Data quality analysis
- Missing-data investigation
- Descriptive statistics
- Time-series analysis
- Statistical trend analysis
- Outlier investigation

### Geospatial Analysis

- Latitude/longitude processing
- Haversine distance
- Spark UDFs
- Pairwise station comparison
- Country-code standardisation
- Choropleth mapping

### Visualisation

- Pandas
- Matplotlib
- Plotly
- Time-series charts
- Distribution plots
- Histograms
- Box plots
- Interactive geographic maps

---

# 28. Repository Structure

```text
01-GHCN-Climate-Analysis/
│
├── README.md
│
├── 1_Processing.ipynb
├── 2_Analysis.ipynb
└── 3_Visualisation.ipynb
```

### `1_Processing.ipynb`

Contains the data preparation workflow, including:

- Dataset exploration
- Schema definition
- Fixed-width metadata parsing
- Country and state processing
- Inventory processing
- Station metadata enrichment
- Data cleaning
- Parquet output
- Station-ID validation
- Join optimisation

### `2_Analysis.ipynb`

Contains the large-scale Spark analysis, including:

- Weather-station statistics
- Country and geographic analysis
- Core weather element counts
- TMAX/TMIN completeness analysis
- New Zealand station selection
- Haversine distance calculations

### `3_Visualisation.ipynb`

Contains the analytical visualisations, including:

- New Zealand TMIN/TMAX analysis
- Station-level temperature trends
- Linear regression
- Seasonal temperature patterns
- Temperature distributions
- Global precipitation trends
- 2024 precipitation analysis
- Outlier investigation
- Global precipitation choropleth

---

# 29. What I Learned

This project provided practical experience working with a dataset far larger than a typical local analytics dataset.

One of the most important lessons was that **big-data analysis requires thinking carefully about how the data is processed, not only what analysis is performed**.

For example, instead of performing an expensive join against billions of daily observations, station IDs could first be reduced to distinct values and compared using a LEFT ANTI JOIN.

Another important lesson was knowing when to use distributed and local tools.

Apache Spark was appropriate for processing billions of observations, while Pandas and visualisation libraries became useful only after the data had been aggregated to a manageable size.

The project also reinforced the importance of data quality. Missing TMIN observations, quality-flagged precipitation records, sparse country coverage, and extreme values all showed why analytical results need to be validated before they are interpreted.

Finally, the project demonstrated how large-scale data engineering and analytics can work together:

```text
Raw Climate Data
        ↓
Distributed Processing
        ↓
Cleaning & Validation
        ↓
Metadata Integration
        ↓
Large-Scale Analysis
        ↓
Statistical / Geospatial Analysis
        ↓
Aggregation
        ↓
Visualisation
        ↓
Interpretation
```

---

## Academic Context

This project was completed as part of **DATA420 - Scalable Data Science** at the **University of Canterbury**.

It demonstrates the practical application of **Apache Spark, PySpark, Azure Blob Storage, large-scale data processing, data quality analysis, geospatial analysis, time-series analysis, statistical analysis, and data visualisation** using a real-world global climate dataset.
