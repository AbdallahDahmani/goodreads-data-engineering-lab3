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
