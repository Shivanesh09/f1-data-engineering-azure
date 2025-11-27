# Configuration Files

## Cluster Configuration
- **File:** `cluster_config.json`
- **Purpose:** Databricks cluster specifications for F1 data processing
- **Usage:** Template for creating compute clusters via Databricks UI or REST API

## Storage Configuration
Storage access configuration is managed within Databricks notebooks for security.

### Authentication Method
- **Type:** SAS Token
- **Storage:** Azure Data Lake Storage Gen2 (ADLS Gen2)
- **Protocol:** Direct path access via `abfss://`
- **Security:** SAS tokens stored securely in Azure Key Vault

### Storage Structure
```
abfss://raw@<storage-account>.dfs.core.windows.net/
├── circuits/
├── constructors/
├── drivers/
├── races/
├── results/
├── lap_times/
├── pit_stops/
├── qualifying/
└── ...

abfss://processed@<storage-account>.dfs.core.windows.net/
├── bronze/    # Raw ingested data
├── silver/    # Cleaned and transformed data
└── gold/      # Business-level aggregations
```

### Configuration Location
- Storage paths and container configurations are defined in notebook configuration files
- SAS tokens accessed via Databricks secrets (integrated with Azure Key Vault)
- No sensitive credentials committed to this repository

## Security Best Practices
⚠️ **Important Security Notes:**
- SAS tokens and storage account names are **NOT** committed to version control
- Configuration values are environment-specific and managed in Databricks workspace
- Access credentials stored in Azure Key Vault and accessed via `dbutils.secrets.get()`

## Usage Example
```python
# Example: Accessing storage in Databricks notebooks
# Configuration managed in dedicated config notebook

# Read from raw storage
df = spark.read.csv("abfss://raw@<account>.dfs.core.windows.net/circuits/circuits.csv")

# Write to processed storage (Delta format)
df.write.format("delta").save("abfss://processed@<account>.dfs.core.windows.net/bronze/circuits")
```
