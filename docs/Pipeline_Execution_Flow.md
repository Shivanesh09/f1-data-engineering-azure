# Pipeline Execution Flow

## Workflow

```
Trigger
  ↓
Get Metadata (Check Data)
  ↓  
If Data Exists?
  YES → Ingest (Bronze) → Transform (Silver) → Analyze (Gold)
  NO  → Skip
```

## Phases

**Phase 1: Check** (1-2 min)  
Get Metadata checks for new race folders

**Phase 2: Bronze** (10-15 min)  
Ingest 8 files if new data exists

**Phase 3: Silver** (15-20 min)  
Clean and standardize data

**Phase 4: Gold** (10-15 min)  
Create analytics tables

## Scenarios

### New Data Found
Duration: 35-50 min  
All layers processed

### No New Data  
Duration: <2 min  
Pipeline skips

### Failure
Review logs, fix issue, rerun

## Dependencies

- Results needs: Circuits, Races, Drivers, Constructors
- Silver needs: All Bronze tables  
- Gold needs: All Silver tables

## Monitor

- Metadata execution time
- Each notebook status
- Row counts per layer
- Total duration
