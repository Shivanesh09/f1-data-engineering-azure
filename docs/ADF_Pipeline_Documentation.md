# Azure Data Factory Orchestration

## Overview
Automated weekly pipeline orchestration using Azure Data Factory to process F1 racing data through Bronze → Silver → Gold layers on Azure Databricks.

## Infrastructure
- **ADF Factory**: databricks-course-adf09
- **Resource Group**: databricks-course-rg  
- **Orchestration**: 3-pipeline architecture with conditional execution

## Pipeline Architecture

### Main Pipeline: `pl_process_formula1_data`
**Purpose**: Master orchestrator coordinating end-to-end ETL workflow

**Flow**:
1. Execute Ingestion Pipeline → 2. Execute Transformation Pipeline

**Schedule**: Weekly execution (configurable trigger)

---

### Ingestion Pipeline: `pl_ingest_formula1_data`
**Purpose**: Conditional data ingestion to Bronze layer

**Key Activities**:
1. **Get Metadata** - Check ADLS Gen2 for new race data folders
2. **If Condition** - Evaluate if new data exists  
   - **True**: Execute 8 Databricks notebooks
   - **False**: Skip processing

**Databricks Notebooks Executed** (Sequential):
- ingest Circuits File → `f1_raw.circuits`
- ingest Races File → `f1_raw.races`  
- ingest Constructors File → `f1_raw.constructors`
- ingest Drivers File → `f1_raw.drivers`
- ingest Results File → `f1_raw.results`
- ingest Pit Stops File → `f1_raw.pit_stops`
- ingest Lap Times File → `f1_raw.lap_times`
- ingest Qualifying File → `f1_raw.qualifying`

---

### Transformation Pipeline: `pl_transform_formula1_data`  
**Purpose**: Transform data through Silver and Gold layers

**Processes**:
- **Silver Layer**: Data cleaning, standardization, deduplication
- **Gold Layer**: Aggregations, analytics, presentation-ready tables

---

## Data Flow

```
Weekly Trigger
    ↓
Check for New Race Data (Get Metadata)
    ↓
If Data Exists?
├─ Yes → Ingest 8 files (Bronze) → Transform (Silver) → Analyze (Gold)
└─ No  → Skip (Pipeline completes in <2 min)
```

## Key Features

✓ **Automated Scheduling**: Weekly execution checks for new race weekends  
✓ **Conditional Logic**: Intelligent processing only when new data available  
✓ **Error Handling**: Built-in retry logic and failure notifications  
✓ **Parameterized**: Date-based processing windows  
✓ **Scalable**: Modular notebook execution on Databricks clusters  

## Execution Metrics

- **Data Availability Check**: 30-60 seconds
- **Bronze Ingestion**: 10-15 minutes (8 notebooks)
- **Silver Transformation**: 15-20 minutes  
- **Gold Analysis**: 10-15 minutes
- **Total Duration**: 35-50 minutes per full execution

## Monitoring

View pipeline runs in ADF Monitor tab:
- Pipeline-level status
- Activity-level execution details
- Databricks notebook logs
- Error messages and retry attempts

## Technologies

- **Azure Data Factory**: Orchestration and scheduling
- **Azure Databricks**: Spark-based data processing  
- **ADLS Gen2**: Scalable data lake storage
- **Delta Lake**: ACID transactions and versioning

---

*For detailed pipeline JSON definitions, see `/adf` folder.*  
*For execution flow diagrams, see `Pipeline_Execution_Flow.md`.*
