# GHCN Climate Data Analysis Using Apache Spark

## Project Overview

This project explores and analyses the Global Historical Climatology Network Daily (GHCN-Daily) dataset using Apache Spark and PySpark.

The main challenge was the scale of the dataset. The complete daily dataset contains more than 3.1 billion weather observations and approximately 13 GB of compressed data. Because this volume of data is not practical to process using normal in-memory approaches, Apache Spark was used for distributed data processing.

The project covers the complete analytical workflow:

1. Understanding the raw data
2. Defining schemas and loading data
3. Cleaning and transforming data
4. Combining multiple metadata sources
5. Optimising Spark operations
6. Analysing weather stations and observations
7. Performing geospatial analysis
8. Analysing New Zealand temperature trends
9. Analysing global precipitation patterns
10. Creating time-series and geospatial visualisations

---

## Technologies Used

- Apache Spark
- PySpark
- Python
- Microsoft Azure
- Azure Blob Storage
- Spark DataFrames
- Pandas
- Matplotlib
- Plotly
- SciPy
- Parquet
- Jupyter Notebook

---

## Dataset

The project uses the Global Historical Climatology Network Daily (GHCN-Daily) dataset.

The dataset contains historical weather observations collected from weather stations around the world.

The main weather elements analysed were:

| Element | Description |
|---|---|
| TMAX | Maximum daily temperature |
| TMIN | Minimum daily temperature |
| PRCP | Daily precipitation |
| SNOW | Daily snowfall |
| SNWD | Snow depth |

Additional metadata was available for:

- Weather stations
- Countries
- US states
- Station element inventories
- Geographic coordinates
- Station elevation
- Station operating periods
- Climate monitoring networks

The data was stored in Azure Blob Storage and accessed using Apache Spark.

---

## Dataset Scale

The complete daily dataset contained:

- **3,139,143,397 weather observations**
- **129,657 weather stations**
- Approximately **13 GB of compressed daily data**
- Historical observations from **1750 to 2025**

This scale made efficient Spark processing important. Large datasets were processed using Spark DataFrames rather than being loaded into local memory.

---

# 1. Data Processing

## Loading the Data

The daily weather observations were stored as CSV files.

I created an explicit PySpark schema containing fields such as:

- Station ID
- Date
- Weather element
- Observation value
- Measurement flag
- Quality flag
- Source flag
- Observation time

Using an explicit schema helped ensure that dates, numeric measurements, and categorical fields were represented using appropriate data types.

---

## Processing Fixed-Width Metadata

The station, country, state, and inventory datasets were stored as fixed-width text files.

Because these files could not be loaded directly like standard CSV files, I read them using:

`Spark.read.text()`

and extracted individual fields using PySpark substring operations.

This allowed information such as station IDs, latitude, longitude, elevation, country codes, station names, and operating periods to be converted into structured Spark DataFrames.

---

## Creating Enriched Station Metadata

Several metadata datasets were combined to create a single enriched station dataset.

The workflow included:

1. Extracting the country code from each station ID
2. Joining stations with country information
3. Joining US stations with state information
4. Aggregating station inventory information
5. Determining each station's first and last active year
6. Counting weather elements recorded by each station
7. Identifying core weather elements
8. Combining all information into one station-level dataset

The resulting dataset contained information such as:

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
- Climate monitoring network information

The enriched station dataset was saved in **Parquet format** to Azure Blob Storage.

Parquet was selected because it provides efficient columnar storage, compression, and schema preservation.

---

# 2. Spark Join Optimisation

One part of the project investigated whether weather observations contained station IDs that were missing from the station metadata.

I initially explored a LEFT JOIN approach.

However, joining the entire multi-billion-row daily dataset with all station metadata would require unnecessary data movement and memory.

A more efficient approach was implemented using a **LEFT ANTI JOIN** on distinct station IDs.

This reduced the amount of data involved in the comparison and provided a more efficient way to identify unmatched stations.

The final check found that all station IDs in the complete daily dataset were represented in the station metadata.

This part of the project helped demonstrate the importance of choosing appropriate Spark operations when working with large datasets.

---

# 3. Weather Station Analysis

After preparing the data, I used PySpark DataFrame operations such as:

- `filter()`
- `groupBy()`
- `agg()`
- `join()`
- `collect_set()`

to investigate the weather station network.

The analysis included:

- Total number of stations
- Active stations
- Climate monitoring network membership
- Northern and Southern Hemisphere distribution
- Country-level station distribution
- US state and territory distribution

The dataset contained **129,657 unique weather stations**.

---

# 4. Geospatial Analysis

I also performed geospatial analysis on New Zealand weather stations.

A **Haversine distance function** was implemented to calculate the geographic distance between two locations using latitude and longitude.

The Python function was registered as a Spark User Defined Function (UDF).

New Zealand stations were then compared using a Spark CROSS JOIN, and the Haversine function was applied to calculate pairwise distances.

This demonstrated how custom geographic calculations can be incorporated into a Spark workflow.

---

# 5. Large-Scale Weather Observation Analysis

The complete daily dataset contained:

**3,139,143,397 observations.**

The five main weather elements were analysed using PySpark.

| Weather Element | Observations |
|---|---:|
| Precipitation (PRCP) | 1,079,767,077 |
| Maximum Temperature (TMAX) | 460,114,659 |
| Minimum Temperature (TMIN) | 458,928,768 |
| Snowfall (SNOW) | 359,249,644 |
| Snow Depth (SNWD) | 300,711,620 |

Precipitation was the most frequently recorded weather element.

---

## Missing TMIN Investigation

An additional data-quality investigation examined situations where a station reported maximum temperature (TMAX) but did not report minimum temperature (TMIN) for the same date.

The data was grouped by:

- Station ID
- Date

The available weather elements for each station-date combination were then compared.

The analysis identified:

- **10,660,214 TMAX observations without matching TMIN**
- **28,754 stations contributing to these observations**

This highlighted the importance of checking data completeness before performing climate analysis.

---

# 6. New Zealand Temperature Analysis

The next stage focused on temperature observations from New Zealand weather stations.

The analysis included:

- TMIN and TMAX
- Station-level temperature trends
- Missing years
- Seasonal patterns
- Temperature distributions
- National temperature trends

The analysed New Zealand data covered the period from **1940 to 2025**.

Fifteen New Zealand weather stations were included in the analysis.

---

## Preparing Temperature Data

The daily dataset was filtered to retain:

- New Zealand stations
- TMIN observations
- TMAX observations

The data was then reshaped so that TMIN and TMAX could be compared for each station and date.

Aggregated Spark results were converted to Pandas only after the dataset had been reduced to a manageable size.

This avoided attempting to load the complete Spark dataset into local memory.

---

## Temperature Trend Analysis

Yearly average TMIN and TMAX values were calculated for individual New Zealand stations.

Linear regression was used to estimate the direction and rate of temperature change over time.

The analysis found that:

**11 of the 15 analysed stations showed increasing trends in both TMIN and TMAX.**

However, station coverage varied considerably, so missing years and differences in station operating periods were considered when interpreting these trends.

---

## Seasonal Temperature Patterns

Monthly averages were calculated to investigate New Zealand's seasonal temperature pattern.

The results showed the expected seasonal cycle:

- Higher temperatures during summer
- Lower temperatures during winter

The analysis also examined temperature distributions for individual weather stations and for New Zealand overall.

---

# 7. Global Precipitation Analysis

The project also investigated global precipitation patterns.

The original dataset contained more than:

**1.07 billion precipitation observations.**

Before analysing rainfall, I performed data-quality checks using the GHCN quality flags.

Records with non-null quality flags were removed from the analytical dataset.

This removed:

**629,024 observations that failed quality checks.**

The cleaned precipitation data was then grouped by:

- Year
- Country

Average daily precipitation was calculated for each country-year combination.

---

# 8. Outlier and Data Quality Investigation

An important part of the analysis was determining whether extreme values represented genuine climate patterns or problems caused by limited observations.

For example, some countries appeared to have extremely high average rainfall values.

Instead of accepting these results directly, I investigated:

- Number of observations
- Quality flags
- Missing values
- Extreme measurements
- Sparse historical coverage

This showed that some apparently extreme country averages were based on very small numbers of observations.

The analysis therefore demonstrated why aggregated statistics need to be interpreted together with data completeness and quality.

---

# 9. 2024 Global Rainfall Analysis

Average daily precipitation was analysed across countries for 2024.

The analysis included:

- Descriptive statistics
- Distribution analysis
- Histograms
- Box plots
- Outlier detection
- Rainiest countries
- Driest countries
- Observation-count validation

The Interquartile Range (IQR) method was used to investigate unusually high precipitation values.

This helped distinguish potentially meaningful values from results affected by sparse observations.

---

# 10. Global Precipitation Map

A global choropleth map was created using Plotly.

Country names required additional cleaning before they could be mapped correctly.

The workflow included:

1. Cleaning country names
2. Removing additional territory descriptions
3. Converting country names to ISO-3 codes
4. Manually handling unmatched locations
5. Connecting precipitation values to geographic locations
6. Creating the interactive choropleth map

The final visualisation showed geographic differences in average precipitation across countries.

---

# Key Learnings

This project gave me practical experience working with data at a scale much larger than typical in-memory analytics projects.

The main areas I developed were:

- Large-scale data processing with Apache Spark
- PySpark DataFrame operations
- Processing billions of records
- Schema design
- Fixed-width file parsing
- Data cleaning and transformation
- Large-scale joins
- Spark query optimisation
- Azure Blob Storage
- Parquet data storage
- Data quality validation
- Statistical analysis
- Time-series analysis
- Geospatial calculations
- Climate data analysis
- Pandas and Matplotlib visualisation
- Plotly geospatial visualisation

One of the most important lessons from the project was that working with large datasets is not only about obtaining results. The choice of data structures, joins, storage formats, aggregation methods, and memory-management strategies can significantly affect whether an analysis is practical.

The project also reinforced the importance of investigating missing data, sparse observations, quality flags, and outliers before interpreting analytical results.

---

# Repository Structure

```text
01-GHCN-Climate-Analysis/
│
├── README.md
├── 1_Processing.ipynb
├── 2_Analysis.ipynb
└── 3_Visualisation.ipynb
