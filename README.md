# f1-data-engineering-azure

## Overview

End-to-end data engineering solution for Formula 1 racing analytics using Azure cloud services. Implements medallion architecture (Bronze → Silver → Gold) with automated orchestration via Azure Data Factory.

## Architecture

- **Ingestion**: CSV/JSON files from Ergast F1 API
- **Storage**: Azure Data Lake Storage Gen2 (ADLS)
- **Processing**: Azure Databricks with PySpark
- **Orchestration**: Azure Data Factory pipelines
- **Format**: Delta Lake tables with ACID transactions

## Data Pipeline

### Bronze Layer (Raw)
- Ingest 8 F1 data entities from source
- Minimal transformation, preserve raw data
- Delta tables in `f1_raw` database

### Silver Layer (Processed)
- Data cleaning and standardization
- Type casting, null handling, deduplication
- Delta tables in `f1_processed` database

### Gold Layer (Analytics)
- Business-level aggregations
- Race results, driver/constructor standings
- Delta tables in `f1_presentation` database

## Azure Data Factory Orchestration

**3-Pipeline Architecture:**

1. **pl_process_formula1_data** - Master orchestrator
2. **pl_ingest_formula1_data** - Conditional Bronze ingestion
3. **pl_transform_formula1_data** - Silver & Gold processing

**Key Features:**
- ✓ Automated weekly scheduling  
- ✓ Get Metadata activity checks for new data
- ✓ Conditional execution (only process if new data exists)
- ✓ 8 Databricks notebooks for ingestion
- ✓ End-to-end execution: 35-50 minutes

[See detailed orchestration docs](/docs/ADF_Pipeline_Documentation.md)

## Project Structure

```
/config         - Databricks cluster configuration
/data          - Data dictionary and schemas  
/docs          - Comprehensive project documentation
/notebooks     - PySpark notebooks (ingestion, transformation, analysis)
/images        - Dashboard screenshots
```

## Technologies

- **Azure Data Factory**: Pipeline orchestration
- **Azure Databricks**: Spark-based processing  
- **Azure Data Lake Gen2**: Scalable storage
- **Delta Lake**: ACID transactions, time travel
- **PySpark**: Distributed data processing
- **Python**: Automation and utilities

## Key Features

- Medallion Architecture (Bronze/Silver/Gold)
- Automated pipeline orchestration
- Incremental data processing
- Data quality checks
- Partitioning by race_year
- Comprehensive documentation

## Documentation

- [ADF Pipeline Documentation](/docs/ADF_Pipeline_Documentation.md)
- [Pipeline Execution Flow](/docs/Pipeline_Execution_Flow.md)
- [Data Dictionary](/data/DATA_DICTIONARY.md)


## Setup

See `/config` folder for Databricks cluster configuration and setup instructions.

## License

MIT
