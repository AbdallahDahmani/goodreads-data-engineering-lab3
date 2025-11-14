# goodreads-data-engineering-lab3

Project Overview
    This project implements a complete data engineering pipeline on Microsoft Azure, processing GoodReads book review      data through a modern lakehouse architecture. The pipeline follows the medallion architecture pattern (Bronze →        Silver → Gold layers) using Azure Databricks, Azure Data Lake Storage Gen2, and Azure Data Factory.


Technologies Used
    Azure Data Lake Storage Gen2 - Data storage and lakehouse foundation
    Azure Databricks - Data processing and transformation engine
    Azure Data Factory - ETL pipeline orchestration
    Delta Lake - ACID transactions and schema enforcement
    PySpark - Distributed data processing
    Python - Data analysis and scripting

Dataset
    The project uses the GoodReads dataset containing:
    Books: 16GB of book metadata (titles, authors, publication details)
    Reviews: User reviews and ratings with text content
    Authors: Author information and biographies

Homework Part 1: Building the Lakehouse
    Objectives
        Set up Azure Data Lake Storage Gen2 with proper container structure
        Ingest raw JSON data into Bronze layer
        Transform data to Parquet format using Azure Data Factory
        Clean and validate data using Databricks
        Create curated Gold layer tables

Implementation Steps
    1. Storage Infrastructure
        Created ADLS Gen2 storage account with hierarchical namespace enabled
        Established container structure following medallion architecture:
        lakehouse/raw/ (Bronze Layer) - Raw JSON files
        lakehouse/processed/ (Silver Layer) - Cleaned Parquet files
        lakehouse/gold/ (Gold Layer) - Curated Delta tables
    2. Data Ingestion
        Downloaded GoodReads datasets from public URLs using wget
        Used azcopy to upload 16GB of data directly to Azure Data Lake
        Generated SAS tokens for secure storage access
        Created separate folders for books, reviews, and authors datasets
    3. Azure Data Factory Pipeline
        Created ADF instance with linked services to ADLS Gen2
        Built copy data pipelines to transform JSON → Parquet
        Configured datasets for source (JSON) and sink (Parquet)
        Implemented error handling for malformed JSON records
        Published all pipeline artifacts to make configurations permanent
    4. Databricks Data Processing
        Set up Databricks workspace and configured compute cluster
        Connected to ADLS using storage account key authentication
        Implemented comprehensive data cleaning:
        Removed records with null primary keys
        Validated rating ranges (1-5 only)
        Filtered out short reviews (<10 characters)
Homework Part 2: Data Cleaning & Transformation
    Objectives
        Perform comprehensive data cleaning equivalent to Microsoft Fabric
        Implement feature engineering and business logic
        Create aggregations for analytical insights
        Generate data quality reports and validation checks
    Implementation Steps
        1. Data Type Standardization
            Converted all ID columns to proper string types
            Ensured numeric columns have correct data types
            Standardized date formats across the dataset
            Added proper data validation constraints
        2. Missing Value Handling
            Removed records with null critical fields (review_id, book_id, user_id, rating, review_text)
            Replaced missing values with appropriate defaults:
            n_votes → 0 (assuming no votes)
            language → "Unknown" (for missing language information)
            Filtered out future dates to ensure data quality
        3. Text Standardization
            Trimmed whitespace from all text fields (review_text, title, name)
            Capitalized book titles and author names for consistency
            Removed very short reviews (<10 characters) as low-quality content
            Added review length as a derived feature
        4. Advanced Feature Engineering
            Sentiment Analysis: Classified reviews as Positive/Negative/Neutral based on rating thresholds
            Spoiler Detection: Used regex patterns to identify reviews containing spoilers
            Review Quality Scoring:
            Flagged detailed reviews (>50 words) as high-quality content
            Identified helpful reviews (>5 votes) based on community engagement
            Text Analytics: Calculated word counts, sentence counts, and readability metrics
        5. Business Aggregations
            Book-level Metrics:
            Average ratings and standard deviation
            Total review counts and unique reviewers
            Sentiment distribution (positive/negative/neutral ratios)
            Engagement metrics (total votes, helpful review counts)
            Content analysis (average review length, spoiler percentages)
    Author-level Metrics:
        Performance across multiple books
        Review volume and reader engagement
        Writing style analysis through text metrics
        Audience sentiment trends
        Deduplicated records based on review_id
        Created Gold layer by joining books, authors, and reviews into a unified dataset


Technical Challenges & Solutions
Challenge 1: Storage Authentication
Problem: Persistent "Invalid configuration value" errors with storage account keys
Solution: Implemented multiple authentication methods including SAS tokens and proper key validation

Challenge 2: Data Quality Issues
Problem: Malformed JSON records and inconsistent data formats in reviews dataset
Solution: Implemented robust error handling in Databricks with PERMISSIVE mode for JSON parsing

Challenge 3: Large Dataset Processing
Problem: 16GB dataset causing memory issues and slow processing
Solution: Used Spark distributed computing with proper partitioning and cluster configuration

Challenge 4: Schema Evolution
Problem: Missing columns and schema mismatches during joins
Solution: Created flexible join logic with column validation and fallback options

Data Quality Metrics
Final Dataset Statistics
Data Retention Rate: >95% after comprehensive cleaning

Quality Score: All validation checks passed

Average Review Length: 150+ characters after filtering

Rating Distribution: Normal distribution centered around 3.5-4.0

Validation Checks Implemented
✅ No null values in primary key columns

✅ All ratings within valid range (1-5)

✅ Minimum review text length (10+ characters)

✅ No future dates in review timestamps

✅ Consistent data types across all columns

✅ No duplicate review records

✅ Valid numeric ranges for vote counts

Business Value Delivered
Analytical Insights
Book Recommendations: Sentiment analysis enables personalized recommendations

Author Performance: Track author engagement and reader satisfaction

Content Quality: Identify high-quality reviews and helpful content

Market Trends: Analyze reading patterns and genre preferences

Operational Benefits
Scalable Architecture: Handles 16GB+ datasets efficiently

Data Quality Framework: Comprehensive validation and monitoring

Reusable Patterns: Modular code for future data projects

Cloud Native: Leverages Azure's fully managed services

Setup and Execution
Prerequisites
Azure subscription with appropriate permissions

Databricks workspace configured

ADLS Gen2 storage account

Azure Data Factory instance

Execution Order
Run lab_3_homework_1.py to set up infrastructure and initial pipeline

Execute Azure Data Factory pipelines for ETL

Run lab_3_homework_2.py for data transformations and analytics

Verify results in the gold layer tables

Configuration
Update the following in both scripts:

Storage account name and key

Resource group information

Database names and table paths

Results and Outputs
Gold Layer Deliverables
curated_reviews_gold - Unified dataset with books, authors, and reviews

book_aggregations - Analytical metrics at book level

author_aggregations - Performance metrics at author level

cleaned_reviews - Processed and validated review data

Analytical Capabilities
Sentiment analysis across book genres

Author performance benchmarking

Review quality assessment

Reader engagement tracking

Future Enhancements
Real-time Processing: Add streaming capabilities for new reviews

Machine Learning: Implement recommendation engines and predictive analytics

Monitoring: Add data quality monitoring and alerting systems

CI/CD: Automate pipeline deployment and testing

Data Governance: Implement data lineage and cataloging

Conclusion
This project successfully demonstrates a production-ready data engineering pipeline on Azure, handling large-scale data processing with comprehensive quality checks. The implementation follows industry best practices for cloud data engineering and provides a solid foundation for analytical applications.

The modular architecture allows for easy extension and maintenance, while the comprehensive documentation ensures knowledge transfer and operational support.

----------------------------------------------------------------------------------------------------------------------

# Lab 4: Text Feature Engineering - GoodReads Dataset
Overview
This project implements comprehensive text feature engineering for the Goodreads book reviews dataset as part of DSAI 3202 - Data Science in the Cloud. The pipeline transforms raw review text into structured numerical features suitable for machine learning models.

Features Implemented
1. Basic Text Features
review_length_words: Number of words in each review

review_length_chars: Number of characters in each review

sentence_count: Approximate number of sentences

avg_word_length: Average length of words in characters

2. Sentiment Analysis Features
VADER Sentiment:

vader_pos: Positive sentiment intensity (0.0-1.0)

vader_neg: Negative sentiment intensity (0.0-1.0)

vader_neu: Neutral sentiment intensity (0.0-1.0)

vader_compound: Overall sentiment score (-1.0 to 1.0)

TextBlob Sentiment:

blob_polarity: Sentiment polarity (-1.0 to 1.0)

blob_subjectivity: Subjectivity score (0.0-1.0)

3. TF-IDF Features
1000-dimensional sparse vectors using HashingTF

English stop words removal

Tokenization and text normalization

Captures term importance across the corpus

4. N-gram Features
Bigram sequences to capture contextual phrases

Identifies common word pairs and expressions

5. Readability Features
flesch_reading_ease: Readability score (higher = easier to read)

flesch_kincaid_grade: US grade level required (higher = more complex)

smog_index: Complexity measure for technical texts

Data Pipeline Architecture
Step 1: Data Loading
Load curated reviews from Gold layer (Delta format)

Source: abfss://lakehouse@goodreadsreviews60301511.dfs.core.windows.net/gold/curated_reviews/

Step 2: Data Splitting (Prevents Data Leakage)
Training: 70% - Used for fitting all transformers

Validation: 15% - Used for model tuning and feature selection

Test: 15% - Held out for final evaluation

Step 3: Text Cleaning
Convert to lowercase for consistency

Remove punctuation and extra whitespace

Replace URLs with <URL> placeholders

Trim and validate text length (>10 characters)

Step 4: Feature Extraction
Apply all feature engineering techniques in parallel

Fit transformers ONLY on training data

Transform validation and test sets using fitted objects

Step 5: Feature Assembly
Combine all numerical features into final feature vectors

Preserve metadata columns (review_id, book_id, user_id, rating)

Step 6: Data Persistence
Save to Delta format in feature_v2 directory

Separate folders for train, validation, and test sets

Data Leakage Prevention
✅ Critical Security Measure: All transformers fitted ONLY on training data
✅ Validation/Test Integrity: Transformed using fitted objects only
✅ Proper Pipeline Design: Data split before any feature engineering
✅ No Information Leakage: Validation/test data never used in training proces

Technologies Used
Azure Databricks: Distributed computing platform

PySpark: Large-scale data processing

NLTK: Natural language processing toolkit

TextBlob: Simplified text processing

Transformers: Hugging Face library for embeddings

Delta Lake: Reliable data storage format

Key Functions:
clean_text(): Text normalization and cleaning

extract_basic_features(): Basic text statistics

add_sentiment_features(): Sentiment analysis

tfidf_pipeline: TF-IDF vectorization

add_readability_features(): Readability metrics

Results Summary
Feature Statistics (Training Data):
Average words per review: ~131 words

Average characters per review: ~726 characters

Average sentiment score: Slightly positive overall

Readability level: Appropriate for general audience

Output Datasets:
Training: Features fitted and transformed

Validation: Features transformed using training fittings

Test: Features transformed using training fittings (held out)

Quality Assurance:
No null values in critical columns

All ratings within valid range (1-5)

Minimum review length enforced

Proper data types maintained

Key Achievements
Comprehensive Feature Set: Multiple dimensions of text analysis

Production-Ready Pipeline: Scalable and maintainable code

Data Leakage Prevention: Robust pipeline design

Cloud-Native Implementation: Azure Databricks and ADLS integration

Reproducible Results: Consistent feature engineering process

Next Steps
This feature-rich dataset is now ready for:

Machine learning model training

Feature selection and importance analysis

Model validation and hyperparameter tuning

Production deployment in Azure ML
