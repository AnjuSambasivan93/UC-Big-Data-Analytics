# UC Big Data Analytics

A collection of big data analytics and machine learning projects completed as part of my Master of Applied Data Science at the University of Canterbury.

These projects focus on processing and analysing large-scale datasets using Apache Spark and PySpark, with data stored and processed in Microsoft Azure. They cover distributed data processing, data cleaning, exploratory analysis, machine learning, geospatial and time-series analysis, and recommendation systems.

## Projects

### 1. GHCN Climate Data Analysis

Large-scale analysis of the Global Historical Climatology Network (GHCN) dataset containing more than 3.1 billion historical weather observations.

The project includes:

- Large-scale data processing with PySpark
- Weather station metadata integration
- Data quality and completeness analysis
- Spark join optimisation
- Geospatial analysis
- New Zealand temperature trend analysis
- Global precipitation analysis
- Time-series and geographic visualisation

**Technologies:** Apache Spark, PySpark, Python, Azure Blob Storage, Pandas, Matplotlib, Plotly, Parquet

[View GHCN Climate Analysis](./01-GHCN-Climate-Analysis)

---

### 2. Million Song Dataset Analysis

Large-scale music analytics and machine learning project using the Million Song Dataset and Taste Profile user-listening data.

The project includes:

- Large-scale audio feature processing
- Feature engineering and selection
- Binary genre classification
- Multiclass genre classification
- Logistic Regression, Random Forest and Gradient-Boosted Trees
- Class imbalance handling
- Collaborative filtering
- ALS-based song recommendation
- Recommendation evaluation using Precision@10, NDCG and MAP

**Technologies:** Apache Spark, PySpark, Spark ML, Python, Azure Blob Storage, Machine Learning, ALS, Collaborative Filtering

[View Million Song Dataset Analysis](./02-Million-Song-Analysis)

---

## Technologies

- Apache Spark
- PySpark
- Python
- Spark ML
- Microsoft Azure
- Azure Blob Storage
- Pandas
- Matplotlib
- Plotly
- Machine Learning
- Data Processing
- Geospatial Analysis
- Time-Series Analysis
- Collaborative Filtering

## Key Skills Demonstrated

These projects demonstrate practical experience in:

- Processing datasets containing millions and billions of records
- Distributed data processing with Apache Spark
- Building PySpark data processing workflows
- Cleaning, transforming and validating large datasets
- Working with cloud-hosted data in Azure
- Optimising Spark operations for large-scale analysis
- Performing exploratory and statistical analysis
- Building and evaluating machine learning models
- Working with imbalanced classification problems
- Developing recommendation systems using collaborative filtering
- Performing geospatial and time-series analysis
- Converting aggregated Spark results into analytical visualisations

## Repository Structure

    UC-Big-Data-Analytics-/
    │
    ├── README.md
    │
    ├── 01-GHCN-Climate-Analysis/
    │   ├── README.md
    │   ├── 1_Processing.ipynb
    │   ├── 2_Analysis.ipynb
    │   └── 3_Visualisation.ipynb
    │
    └── 02-Million-Song-Analysis/
        ├── README.md
        ├── 1_Processing/
        ├── 2_Audio_Similarity/
        └── 3_Song_Recommendations/

## Academic Context

These projects were completed as part of DATA420 during my Master of Applied Data Science at the University of Canterbury.

They demonstrate the application of big data technologies to real-world datasets, covering the workflow from data ingestion and processing through analysis, machine learning, evaluation and visualisation.
