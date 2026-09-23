# Million Song Dataset – Large-Scale Music Analytics & Recommendation System

## Project Overview

This project analyses the **Million Song Dataset (MSD)** using **Apache Spark and PySpark** to explore two major machine learning problems:

1. **Music genre classification using audio features**
2. **Personalised song recommendation using user listening behaviour**

The project combines large-scale data processing, feature engineering, binary and multiclass classification, collaborative filtering, model evaluation, and recommendation-system analysis.

The data was stored in **Azure Blob Storage** and processed using Apache Spark. The project required working with multiple large datasets containing song metadata, audio features, genre labels, and more than 48 million user-song interactions.

A major focus of the project was not only training machine learning models, but also understanding the challenges associated with large-scale data, including:

- class imbalance
- highly correlated features
- sparse user-item interactions
- skewed play counts
- distributed data processing
- partitioning and caching
- model evaluation
- recommendation ranking

---

## Technologies Used

- Python
- Apache Spark
- PySpark
- Spark SQL / DataFrames
- Spark ML
- Microsoft Azure
- Azure Blob Storage
- Pandas
- NumPy
- Matplotlib
- Machine Learning
- Collaborative Filtering
- Alternating Least Squares (ALS)
- Jupyter Notebook

---

# Dataset

The project uses the **Million Song Dataset (MSD)** and several related datasets.

The main MSD contains metadata and analysis information for approximately **one million songs**.

Additional datasets were used for machine learning and recommendation modelling.

### Main Dataset

The main dataset contains information such as:

- Song ID
- Track ID
- Artist information
- Song title
- Year
- Tempo
- Loudness
- Time signature
- Other song and audio metadata

### Audio Feature Datasets

Several audio feature datasets were used to describe the characteristics of each track.

These included:

- Area of Moments
- Linear Predictive Coding (LPC)
- Spectral features
- Timbral features

These numerical features represent characteristics of how songs sound and were used as predictors for genre classification.

### Genre Dataset

The **MSD AllMusic Genre Dataset (MAGD)** was used to associate tracks with music genres.

These genre labels were used as the target variable for the classification models.

### Taste Profile Dataset

The Taste Profile dataset contains user listening behaviour in the form:

```text
user_id | song_id | play_count
```

The dataset contains:

- More than **48 million user-song interactions**
- More than **1 million users**
- Approximately **384,000 songs**

Unlike an explicit rating dataset, users do not directly rate songs. Instead, the number of times a song was played is treated as **implicit feedback**.

---

# Project Workflow

The project was divided into three major stages:

```text
Raw Music Data
      │
      ▼
Data Exploration & Processing
      │
      ├─────────────────────────────┐
      ▼                             ▼
Audio Features                Taste Profile
      │                             │
      ▼                             ▼
Feature Engineering           User/Song Filtering
      │                             │
      ▼                             ▼
Genre Classification          ALS Collaborative Filtering
      │                             │
      ▼                             ▼
Model Evaluation              Recommendation Evaluation
```

---

# 1. Data Processing

The first stage focused on understanding and preparing the datasets stored in Azure Blob Storage.

The datasets were available in different formats and structures, so each dataset required an appropriate loading and preprocessing strategy.

The processing work included:

- Exploring the directory structure
- Investigating dataset sizes and formats
- Counting records
- Loading datasets with Spark
- Defining appropriate schemas
- Cleaning data
- Renaming columns
- Preparing audio features for modelling
- Preparing genre labels
- Preparing user-song interactions

---

## Automatic Schema Creation

The audio feature datasets were accompanied by attribute files describing their column names and data types.

Instead of manually defining every column, the attribute information was used to construct PySpark schemas programmatically.

The general process was:

```text
Attribute File
      ↓
Read feature names and types
      ↓
Map types to PySpark data types
      ↓
Create StructType schema
      ↓
Load audio feature dataset
```

This provided a systematic way to load multiple audio feature datasets.

---

## Column Renaming

The original audio feature names were often long and difficult to use.

A systematic naming strategy was therefore applied so that:

- columns were easier to understand
- columns could be referenced consistently
- features from different datasets could be distinguished
- merged datasets avoided ambiguous column names

This was especially important when combining several audio feature datasets into a single modelling dataset.

---

# 2. Audio Feature Processing

Four major groups of audio features were combined:

- Area of Moments
- LPC
- Spectral
- Timbral

Before modelling, the features were investigated for:

- missing values
- invalid values
- low variance
- feature distributions
- correlations
- redundant variables

---

## Correlation Analysis

Highly correlated features can provide duplicated information and unnecessarily increase model complexity.

A correlation matrix was therefore calculated across the merged audio features.

Feature pairs with correlation:

```text
|correlation| ≥ 0.95
```

were treated as highly redundant.

Examples of features removed included:

- `area_avg_10`
- `spec_spectralcentroid_avg`
- `timbral_chroma_a_min_meanstd`

After feature cleaning, the final audio dataset contained:

**994,594 rows and 112 columns.**

This produced a cleaner feature set for machine learning.

---

# 3. Combining Audio Features and Genre Labels

The cleaned audio dataset was joined with the MSD AllMusic Genre Dataset.

The resulting dataset connected:

```text
Audio characteristics → Music genre
```

This made it possible to investigate whether the characteristics of a track's audio signal could be used to predict its genre.

The genre distribution was strongly imbalanced, meaning some genres contained substantially more examples than others.

This became an important consideration when:

- splitting the data
- resampling the training data
- selecting evaluation metrics
- interpreting model performance

---

# 4. Binary Genre Classification

The first machine learning problem was formulated as a binary classification task:

```text
Electronic vs Other
```

The objective was to determine whether a track could be identified as **Electronic** using only its audio characteristics.

Three Spark ML classification algorithms were investigated:

1. Logistic Regression
2. Random Forest
3. Gradient-Boosted Trees (GBT)

---

## Logistic Regression

Logistic Regression provided a relatively simple and interpretable baseline.

Advantages included:

- fast training
- relatively easy interpretation
- suitability for high-dimensional feature sets
- lower computational complexity

Because Logistic Regression is a linear model, feature preparation and scaling were important considerations.

---

## Random Forest

Random Forest uses an ensemble of decision trees.

It was useful because it can:

- model nonlinear relationships
- handle complex interactions
- work without feature scaling
- provide strong classification performance

Compared with Logistic Regression, it requires more computation and is less straightforward to interpret.

---

## Gradient-Boosted Trees

GBT builds trees sequentially, with later trees attempting to correct errors made by earlier trees.

Advantages included:

- ability to capture nonlinear patterns
- strong predictive capability
- effective separation of complex classes

The main disadvantage was increased training time and computational cost.

---

# 5. Handling Class Imbalance

The binary target was imbalanced because Electronic tracks represented a smaller proportion of the available labelled tracks.

Simply using a random split could therefore create training and test datasets with inconsistent class proportions.

To address this, I used:

- **stratified train/test splitting**
- **resampling strategies**

The goal was to preserve meaningful class representation while avoiding misleading model evaluation.

This was particularly important because high overall accuracy does not necessarily indicate that a model is successfully identifying the minority class.

---

# 6. Binary Model Evaluation

The classification models were evaluated using multiple metrics rather than relying only on accuracy.

Metrics included:

- Accuracy
- Precision
- Recall
- F1 score

Each metric provides different information.

### Accuracy

Measures the overall percentage of predictions that were correct.

### Precision

Measures how many tracks predicted as Electronic were actually Electronic.

### Recall

Measures how many actual Electronic tracks were successfully identified.

### F1 Score

Balances precision and recall and is particularly useful when the target classes are imbalanced.

---

## Binary Classification Findings

The three algorithms demonstrated different strengths.

### Logistic Regression

- Fast to train
- Simple to understand
- Produced useful results with fewer features
- Provided a strong baseline

### Random Forest

- Produced the strongest overall accuracy
- Provided the strongest precision among the compared models
- Handled nonlinear relationships effectively

### Gradient-Boosted Trees

- Produced the strongest recall for identifying Electronic tracks
- Provided strong class separation
- Required more training time

The comparison demonstrated why model selection should not be based on accuracy alone.

Different models may be preferable depending on whether the goal is to maximise precision, recall, computational efficiency, or interpretability.

---

# 7. Multiclass Genre Classification

The project was then extended from:

```text
Electronic vs Other
```

to:

```text
Multiple Music Genres
```

This was considerably more challenging because the model had to distinguish between many genres instead of only two classes.

The genre labels were converted into integer labels so that they could be used by Spark ML.

---

## Multiclass Challenges

The multiclass problem introduced several challenges:

- large differences in genre frequency
- rare genres with limited training examples
- overlapping audio characteristics
- increased classification complexity
- higher computational requirements

Models generally performed better on common genres and struggled with genres containing fewer observations.

---

# 8. One-vs-Rest Classification

A **One-vs-Rest** strategy was explored for multiclass classification.

For each genre, the model effectively learns:

```text
Genre X vs All Other Genres
```

For example:

```text
Electronic vs Rest
Rock vs Rest
Pop vs Rest
Jazz vs Rest
...
```

The individual classifiers can then be combined to generate the final genre prediction.

This approach allowed binary classifiers to be extended to multiclass prediction.

---

# 9. Grouping Genres

Because some genres had relatively few observations, the original genre structure created a difficult class-imbalance problem.

To investigate whether a simpler target structure could improve performance, related genres were grouped into broader categories.

This reduced the number of classes and increased the amount of training data available within each broader group.

After grouping and resampling, classification performance improved.

The grouped-genre models achieved approximately:

| Model | Accuracy |
|---|---:|
| GBTClassifier | 71% |
| Logistic Regression | 75% |

This demonstrated the importance of target definition and class distribution in multiclass machine learning.

---

# 10. Hyperparameter Analysis

The project also investigated important hyperparameters for:

- Logistic Regression
- Random Forest
- GBTClassifier

The analysis considered how model behaviour could change based on parameters controlling:

- regularisation
- number of iterations
- tree depth
- number of trees
- learning rate
- decision thresholds

---

## Cross-Validation

Cross-validation was considered as a method for selecting better hyperparameter combinations.

The general workflow is:

```text
Training Data
      ↓
Split into folds
      ↓
Train model on multiple combinations
      ↓
Evaluate each combination
      ↓
Compare validation performance
      ↓
Select better parameters
```

This provides a more reliable way to evaluate parameter combinations than choosing values based on a single train/test split.

The experiments showed that parameter and threshold changes could meaningfully affect the balance between precision and recall.

---

# 11. Song Recommendation System

The second major machine learning component of the project was a personalised song recommendation system.

This used the **Taste Profile dataset**, containing more than 48 million user-song play interactions.

The objective was different from genre classification.

Instead of predicting:

```text
What genre is this song?
```

the recommendation model attempted to answer:

```text
What songs is this user likely to listen to?
```

---

# 12. Understanding User Listening Behaviour

Before training the recommendation model, the distribution of user activity and song popularity was analysed.

The dataset showed a strong **long-tail distribution**.

This means:

- a relatively small number of songs were extremely popular
- many songs were played infrequently
- some users were extremely active
- many users interacted with relatively few songs

These patterns are important because recommendation algorithms depend on shared interactions between users and items.

---

# 13. Repartitioning and Caching

The Taste Profile dataset contained approximately **48 million rows** stored across compressed files.

Because the data was repeatedly used for operations such as:

- filtering
- joins
- aggregations
- model training

Spark partitioning and caching strategies were considered.

The data was repartitioned to improve workload distribution across Spark tasks.

Caching was also used after important processing stages so that Spark did not need to repeatedly recompute the same transformations during modelling.

This demonstrated the importance of considering computational efficiency as well as analytical correctness when working with distributed data.

---

# 14. Filtering Users and Songs

Very inactive users and extremely rare songs provide limited information for collaborative filtering.

The data was therefore filtered using thresholds:

```text
Song interactions >= 20
User song interactions >= 20
```

The purpose was to:

- reduce noise
- reduce sparsity
- retain users with useful listening history
- retain songs with sufficient interaction information
- improve the data available to ALS

After filtering, the user-item matrix became denser, although it remained highly sparse.

---

# 15. Play Count Normalisation

Raw play counts were highly skewed.

Most interactions had relatively small play counts, while a small number had extremely large values.

For example, the maximum observed play count reached thousands of plays.

Using these raw values directly can allow extreme observations to have excessive influence on model training.

To reduce this effect, I created a transformed confidence value:

```text
confidence = log(1 + play_count)
```

For example:

```text
play_count = 1
confidence ≈ 0.69

play_count = 9667
confidence ≈ 9.18
```

This substantially compresses extreme play-count values while preserving the fact that larger counts represent stronger interactions.

Both the raw and normalised approaches were evaluated.

---

# 16. Collaborative Filtering with ALS

The recommendation system was developed using **Alternating Least Squares (ALS)** from Spark ML.

ALS is a matrix-factorisation approach.

Conceptually, the user-song interaction matrix is decomposed into latent representations for:

```text
Users → latent preference vectors

Songs → latent characteristic vectors
```

Songs can then be recommended based on how well their latent representations match a user's learned preferences.

This allows recommendations to be generated without requiring explicit star ratings.

---

# 17. Train/Test Strategy for Recommendation

Recommendation systems require special care when creating training and test datasets.

A test user must also have interactions in the training dataset.

Otherwise, the model has no historical information from which to learn that user's preferences.

The split was therefore designed to ensure that test users retained training interactions while keeping the selection as random as practical.

This addresses the **cold-start problem** for evaluation users.

---

# 18. Recommendation Evaluation

The recommendation model was evaluated using ranking metrics at:

```text
K = 10
```

The three primary metrics were:

- Precision@10
- NDCG@10
- MAP@10

---

## Precision@10

Precision@10 measures how many of the ten recommended songs were relevant to the user.

---

## NDCG@10

Normalized Discounted Cumulative Gain considers both:

- whether relevant songs were recommended
- where those songs appeared in the ranked recommendation list

Relevant recommendations appearing near the top receive greater weight.

---

## MAP@10

Mean Average Precision measures recommendation quality across users while considering the ranking positions of relevant items.

---

# 19. Recommendation Results

Two ALS approaches were compared:

1. ALS using raw play counts
2. ALS using normalised confidence values

The results were:

| Metric | Normalised Confidence | Raw Play Count |
|---|---:|---:|
| Precision@10 | **0.0297** | 0.0252 |
| NDCG@10 | **0.0370** | 0.0316 |
| MAP@10 | **0.0116** | 0.0101 |

The normalised confidence approach performed better across all three ranking metrics.

However, overall recommendation performance remained limited.

This is an important result rather than something to hide: offline recommendation systems can be difficult to evaluate when the interaction matrix is extremely sparse.

---

# 20. Recommendation Sparsity

Even after filtering, the user-song interaction matrix had a sparsity/density level of approximately:

```text
0.04241%
```

This means only a very small proportion of all possible user-song combinations contained observed interactions.

The limited overlap between users and songs makes collaborative filtering difficult because the model has fewer shared interaction patterns from which to learn.

Factors affecting recommendation performance included:

- large number of songs
- relatively limited user activity
- highly sparse interactions
- popularity imbalance
- limited overlap between users
- use of play counts without richer contextual information

Despite these limitations, the ALS model was able to learn patterns and generate personalised recommendations, particularly for more active users.

---

# 21. Real-World Recommendation Evaluation

Offline metrics are useful, but they do not completely measure whether users actually enjoy recommendations.

A production recommendation system could therefore be evaluated using **A/B testing**.

Users could be randomly assigned to different recommendation models and compared using behavioural metrics such as:

- Click-through rate
- Recommended-song plays
- Skip rate
- Full-song plays
- Listening time
- Likes
- Saves
- Engagement
- Precision@K
- NDCG@K
- MAP@K

A burn-in period could also be used before comparing models so that each recommendation model has sufficient interaction data.

---

# Key Findings

## Audio Classification

- Audio characteristics can provide useful information for music genre prediction.
- Feature cleaning and correlation analysis reduced redundant information.
- Binary classification produced different trade-offs between models.
- Random Forest provided strong accuracy and precision.
- GBTClassifier provided stronger recall for Electronic tracks.
- Logistic Regression offered faster and simpler modelling.
- Class imbalance significantly affected classification performance.

## Multiclass Classification

- Predicting many individual genres was considerably harder than binary classification.
- Common genres were easier to classify than rare genres.
- Grouping related genres reduced class imbalance.
- Grouped GBTClassifier achieved approximately **71% accuracy**.
- Grouped Logistic Regression achieved approximately **75% accuracy**.

## Recommendation System

- User-song listening behaviour showed a strong long-tail distribution.
- Filtering inactive users and rare songs reduced sparsity.
- Log transformation reduced the influence of extreme play counts.
- ALS successfully generated personalised song recommendations.
- Normalised confidence performed better than raw play counts across Precision@10, NDCG@10 and MAP@10.
- Overall ranking metrics remained low because the user-item matrix was extremely sparse.

---

# Key Skills Demonstrated

This project demonstrates practical experience with:

### Big Data

- Apache Spark
- PySpark
- Distributed data processing
- Spark DataFrames
- Azure Blob Storage
- Partitioning
- Caching
- Large-scale joins and aggregations

### Data Preparation

- Schema creation
- Data cleaning
- Column standardisation
- Feature engineering
- Correlation analysis
- Feature selection
- Data transformation

### Machine Learning

- Logistic Regression
- Random Forest
- Gradient-Boosted Trees
- Binary classification
- Multiclass classification
- One-vs-Rest classification
- Stratified sampling
- Resampling
- Hyperparameter analysis
- Cross-validation
- Model evaluation

### Recommendation Systems

- Collaborative filtering
- Alternating Least Squares
- Implicit feedback
- User-item interaction analysis
- Sparsity analysis
- Ranking metrics
- Recommendation evaluation

### Analytical Thinking

- Class imbalance analysis
- Model trade-off evaluation
- Data sparsity investigation
- Long-tail distribution analysis
- Computational optimisation
- Interpretation of model limitations

---

# Repository Structure

```text
02-Million-Song-Analysis/
│
├── README.md
│
├── 1_Processing/
│
├── 2_Audio_Similarity/
│   │
│   ├── Q1_AudioSimilarity.ipynb
│   │
│   ├── Q2_AudioSimilarity/
│   │   ├── 1_Logistic_Regression/
│   │   ├── 2_Random_Forest/
│   │   └── 3_GBTClassifier/
│   │
│   ├── Q3_AudioSimilarity/
│   └── Q4_Log_Reg_Hyperparameter/
│
└── 3_Song_Recommendations/
```

---

# What I Learned

This project provided practical experience working with machine learning at a much larger scale than a typical local-data analysis project.

One of the main lessons was that successful machine learning depends heavily on **data preparation and problem formulation**, not only on choosing an algorithm.

For genre classification, class imbalance, correlated features, feature scaling, resampling, and target definition had a significant impact on model behaviour.

For recommendation modelling, the main challenge was data sparsity. Even with more than 48 million interactions, the number of possible user-song combinations was much larger, resulting in a highly sparse interaction matrix.

The project also demonstrated the importance of computational decisions when working with large datasets. Spark operations such as repartitioning, caching, filtering, and aggregation had to be considered carefully to make the workflow practical.

Overall, the project provided hands-on experience across the complete workflow:

```text
Large-Scale Data
        ↓
Data Processing
        ↓
Feature Engineering
        ↓
Machine Learning
        ↓
Model Evaluation
        ↓
Recommendation System
        ↓
Interpretation of Results
```

---

## Academic Context

This project was completed as part of **DATA420** at the **University of Canterbury**.

The project demonstrates the practical application of **Apache Spark, PySpark, Azure Blob Storage, machine learning, and collaborative filtering** to a large-scale real-world dataset.
