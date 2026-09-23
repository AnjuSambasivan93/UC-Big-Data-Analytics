# Global Climate Data Analysis Using Apache Spark

## Overview

This project analyses large-scale historical climate data from the Global Historical Climatology Network Daily (GHCN-Daily) dataset.

The project focuses on processing, cleaning, analysing, and visualising global weather observations using Apache Spark and PySpark. The dataset contains more than 3.13 billion weather observations from stations around the world, covering records from 1750 to 2025.

The analysis includes weather station metadata, temperature trends in New Zealand, global precipitation patterns, data quality investigation, and geospatial analysis.

## Technologies Used

- Python
- PySpark
- Apache Spark
- Azure Blob Storage
- Spark DataFrames
- Pandas
- Matplotlib
- Plotly
- Parquet
- Jupyter Notebook

## Dataset

The project uses the GHCN-Daily climate dataset.

The data includes:

- Maximum temperature (TMAX)
- Minimum temperature (TMIN)
- Precipitation (PRCP)
- Snowfall (SNOW)
- Snow depth (SNWD)
- Weather station metadata
- Country and state information
- Station inventory information

The complete daily dataset contains:

- 3,139,143,397 weather observations
- 129,657 weather stations
- Approximately 13 GB of compressed daily data
- Historical observations from 1750 to 2025

## Data Processing

PySpark was used to process the large dataset efficiently.

The processing workflow included:

- Defining schemas for weather observations
- Parsing fixed-width metadata files
- Converting columns to appropriate data types
- Handling missing values
- Joining station, country, state, and inventory datasets
- Creating enriched weather-station metadata
- Validating station IDs using Spark joins
- Saving processed datasets in Parquet format

For large-scale validation, LEFT ANTI JOIN was used to efficiently identify station IDs that did not match the station metadata.

## Climate Data Analysis

The analysis explored several aspects of the global climate dataset.

### Weather Station Analysis

The project analysed:

- Number of weather stations
- Active stations
- Climate monitoring network membership
- Northern and Southern Hemisphere distribution
- Country-level station distribution
- US state and territory station distribution

The dataset contains 129,657 unique weather stations.

### Geospatial Analysis

A Haversine distance function was implemented as a Spark UDF to calculate geographic distances between weather stations.

The analysis compared New Zealand weather stations and identified the closest station pair in the analysed station data.

### Large-Scale Weather Observation Analysis

The full daily dataset contains:

| Weather Element | Number of Observations |
|---|---:|
| Precipitation (PRCP) | 1,079,767,077 |
| Maximum Temperature (TMAX) | 460,114,659 |
| Minimum Temperature (TMIN) | 458,928,768 |
| Snowfall (SNOW) | 359,249,644 |
| Snow Depth (SNWD) | 300,711,620 |

The analysis also identified more than 10.6 million TMAX observations without a corresponding TMIN observation.

## New Zealand Temperature Analysis

Temperature data from 15 New Zealand weather stations was analysed.

The analysis included:

- TMIN and TMAX data preparation
- Missing-data investigation
- Yearly temperature trends
- Monthly seasonal patterns
- Station-level temperature distributions
- Linear regression trend analysis

The available New Zealand temperature records covered 1940–2025.

The analysis found that 11 of the 15 analysed stations showed increasing trends in both minimum and maximum temperatures.

## Global Precipitation Analysis

More than 1.07 billion precipitation observations were analysed.

Data quality checks were performed using the GHCN quality flags, and 629,024 records that failed quality checks were removed before the aggregated rainfall analysis.

The analysis included:

- Global precipitation trends
- Country-level average rainfall
- Outlier investigation
- Observation-count validation
- Rainiest and driest countries
- 2024 precipitation distribution
- Global rainfall visualisation

A Plotly choropleth map was created to visualise average daily precipitation across countries in 2024.

## Key Skills Demonstrated

This project demonstrates practical experience with:

- Large-scale data processing
- Apache Spark and PySpark
- Cloud-based data storage
- Data cleaning and transformation
- Spark DataFrame operations
- Data integration and joins
- Data quality validation
- Time-series analysis
- Geospatial analysis
- Statistical analysis
- Data visualisation
- Working with billions of records

## Project Structure

The repository contains the Jupyter notebooks used for data processing, analysis, and visualisation.

The project is organised into the main stages of:

1. Data processing and metadata preparation
2. Exploratory and statistical analysis
3. New Zealand temperature analysis
4. Global precipitation analysis
5. Data visualisation

## About

This project was completed as part of DATA420 at the University of Canterbury.

It demonstrates the use of Apache Spark and PySpark for analysing large datasets that are not practical to process using traditional in-memory data analysis workflows.
