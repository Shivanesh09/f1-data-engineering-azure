# Formula 1 Data Dictionary

## Overview
This data dictionary describes the Formula 1 racing data schema used in this project. The data follows a medallion architecture pattern with Bronze (raw), Silver (cleaned), and Gold (aggregated) layers stored in Azure Data Lake Storage Gen2.

## Data Source
**Source**: Ergast Developer API (Formula 1 Historical Data)  
**Format**: CSV files  
**Time Period**: 1950 - Present  
**Update Frequency**: Race season updates

## Architecture Layers

### Bronze Layer (f1_raw)
Raw ingested data with minimal transformation
- Located in ADLS container: `raw/`
- Format: Delta Lake tables
- Retention: Full historical data

### Silver Layer (f1_processed)
Cleaned and standardized data
- Located in ADLS container: `processed/silver/`
- Format: Delta Lake tables with schema enforcement
- Transformations: Data type casting, null handling, deduplication

### Gold Layer (f1_presentation)
Business-level aggregations for analytics
- Located in ADLS container: `processed/gold/`
- Format: Optimized Delta Lake tables
- Use case: Dashboards, reporting, ML features

---

## Table Schemas

### 1. circuits
Information about Formula 1 racing circuits worldwide.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `circuitId` | INT | Primary key, unique circuit identifier | 1 |
| `circuitRef` | STRING | Unique circuit reference code | albert_park |
| `name` | STRING | Official circuit name | Albert Park Grand Prix Circuit |
| `location` | STRING | City/location name | Melbourne |
| `country` | STRING | Country name | Australia |
| `lat` | FLOAT | Latitude coordinate | -37.8497 |
| `lng` | FLOAT | Longitude coordinate | 144.968 |
| `alt` | INT | Altitude in meters | 10 |
| `url` | STRING | Wikipedia page URL | http://en.wikipedia.org/wiki/Melbourne_Grand_Prix_Circuit |

**Business Use**: Track location analysis, geographical distribution of races

---

### 2. constructors
Formula 1 racing teams (constructors) information.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `constructorId` | INT | Primary key, unique constructor identifier | 1 |
| `constructorRef` | STRING | Unique constructor reference code | mclaren |
| `name` | STRING | Constructor team name | McLaren |
| `nationality` | STRING | Constructor nationality | British |
| `url` | STRING | Wikipedia page URL | http://en.wikipedia.org/wiki/McLaren |

**Business Use**: Team performance analysis, constructor championship standings

---

### 3. drivers  
Formula 1 driver biographical and career information.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `driverId` | INT | Primary key, unique driver identifier | 1 |
| `driverRef` | STRING | Unique driver reference code | hamilton |
| `number` | INT | Permanent driver number (NULL for pre-2014) | 44 |
| `code` | STRING | Three-letter driver code | HAM |
| `forename` | STRING | Driver first name | Lewis |
| `surname` | STRING | Driver last name | Hamilton |
| `dob` | DATE | Date of birth | 1985-01-07 |
| `nationality` | STRING | Driver nationality | British |
| `url` | STRING | Wikipedia page URL | http://en.wikipedia.org/wiki/Lewis_Hamilton |

**Business Use**: Driver statistics, age analysis, nationality trends

---

### 4. races
Scheduled Formula 1 race events by season.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `raceId` | INT | Primary key, unique race identifier | 1 |
| `year` | INT | Season year (foreign key to seasons) | 2021 |
| `round` | INT | Race round number in season | 1 |
| `circuitId` | INT | Foreign key link to circuits table | 1 |
| `name` | STRING | Official race name | Australian Grand Prix |
| `date` | DATE | Race date | 2021-03-21 |
| `time` | TIME | Race start time (UTC) | 05:00:00 |
| `url` | STRING | Wikipedia page URL | http://en.wikipedia.org/wiki/2021_Australian_Grand_Prix |

**Business Use**: Race calendar, season scheduling, historical race tracking

---

### 5. results
Race finishing results and performance metrics.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `resultId` | INT | Primary key, unique result identifier | 1 |
| `raceId` | INT | Foreign key link to races table | 1 |
| `driverId` | INT | Foreign key link to drivers table | 1 |
| `constructorId` | INT | Foreign key link to constructors table | 1 |
| `number` | INT | Driver race number | 44 |
| `grid` | INT | Starting grid position (0 = pitlane start) | 1 |
| `position` | INT | Final finishing position (NULL if DNF) | 1 |
| `positionText` | STRING | Position as string (includes DNF codes) | 1 |
| `positionOrder` | INT | Position for ordering (DNF drivers ordered by laps) | 1 |
| `points` | FLOAT | Championship points earned | 25.0 |
| `laps` | INT | Total laps completed | 58 |
| `time` | STRING | Finishing time or gap | 1:34:50.616 |
| `milliseconds` | INT | Finishing time in milliseconds | 5690616 |
| `fastestLap` | INT | Lap number of fastest lap | 39 |
| `rank` | INT | Fastest lap rank among all drivers | 1 |
| `fastestLapTime` | STRING | Fastest lap time | 1:27.453 |
| `fastestLapSpeed` | STRING | Fastest lap speed (km/h) | 213.874 |
| `statusId` | INT | Foreign key link to status table | 1 |

**Position Text Codes**:
- `1-20`: Finishing position
- `R`: Retired
- `D`: Disqualified  
- `E`: Excluded
- `W`: Withdrew
- `F`: Failed to qualify
- `N`: Not classified

**Business Use**: Race winner analysis, points scoring, performance metrics, DNF analysis

---

### 6. qualifying
Qualifying session results and lap times.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `qualifyId` | INT | Primary key, unique qualifying identifier | 1 |
| `raceId` | INT | Foreign key link to races table | 1 |
| `driverId` | INT | Foreign key link to drivers table | 1 |
| `constructorId` | INT | Foreign key link to constructors table | 1 |
| `number` | INT | Driver number | 44 |
| `position` | INT | Qualifying position | 1 |
| `q1` | STRING | Q1 session lap time | 1:26.572 |
| `q2` | STRING | Q2 session lap time | 1:25.187 |
| `q3` | STRING | Q3 session lap time | 1:24.125 |

**Note**: Q2 and Q3 are NULL if driver didn't advance to those sessions

**Business Use**: Qualifying performance analysis, grid position prediction, session comparison

---

### 7. lapTimes
Detailed lap-by-lap timing data for each driver.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `raceId` | INT | Primary key, foreign key link to races table | 1 |
| `driverId` | INT | Primary key, foreign key link to drivers table | 1 |
| `lap` | INT | Primary key, lap number | 1 |
| `position` | INT | Driver position during this lap | 1 |
| `time` | STRING | Lap time (MM:SS.mmm format) | 1:43.762 |
| `milliseconds` | INT | Lap time in milliseconds | 103762 |

**Business Use**: Lap time analysis, race pace comparison, tire degradation analysis, strategy evaluation

---

### 8. pitStops
Pit stop timing and duration data.

| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| `raceId` | INT | Primary key, foreign key link to races table | 1 |
| `driverId` | INT | Primary key, foreign key link to drivers table | 1 |
| `stop` | INT | Primary key, stop number for this driver | 1 |
| `lap` | INT | Lap number when pit stop occurred | 12 |
| `time` | TIME | Time of day when stop occurred | 13:52:25 |
| `duration` | STRING | Duration of pit stop (seconds) | 21.783 |
| `milliseconds` | INT | Duration in milliseconds | 21783 |

**Business Use**: Pit stop strategy analysis, team performance, fastest pit stop awards

---

## Data Quality Notes

### Missing Data Patterns
- **Driver numbers**: NULL for races before 2014 (permanent numbers introduced in 2014)
- **Driver codes**: NULL for early-era drivers
- **Lap times**: Only available from 1996 onwards
- **Pit stops**: Only available from 2011 onwards
- **Qualifying Q2/Q3**: NULL if driver eliminated in earlier session

### Data Formats
- **Dates**: ISO 8601 format (YYYY-MM-DD)
- **Times**: ISO 8601 format (HH:MM:SS)
- **Lap times**: MM:SS.mmm format
- **Encoding**: UTF-8

### Special Values
- **Grid position 0**: Starting from pit lane
- **Position NULL**: Did not finish (DNF)
- **Time NULL**: Race not completed

---

## Relationships

```
races
├── circuitId → circuits.circuitId
├── results → results.raceId
│   ├── driverId → drivers.driverId
│   └── constructorId → constructors.constructorId
├── qualifying → qualifying.raceId
│   ├── driverId → drivers.driverId
│   └── constructorId → constructors.constructorId
├── lapTimes → lapTimes.raceId
│   └── driverId → drivers.driverId
└── pitStops → pitStops.raceId
    └── driverId → drivers.driverId
```

---

## Data Attribution

This data dictionary is based on the **Ergast Database User Guide** by Chris Newell.  

**Source**: Ergast Developer API (http://ergast.com/mrd/)  
**License**: Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License  
**Original Guide Author**: Chris Newell  
**Date**: January 31, 2021

This project uses the Ergast F1 data for educational and portfolio purposes in compliance with the CC BY-NC-SA 3.0 license.

---

## Storage Information

**Production Data Location**: Azure Data Lake Storage Gen2 (ADLS Gen2)  
**Access Method**: Databricks notebooks with SAS token authentication  
**Security**: Credentials managed via Azure Key Vault  
**Format**: Delta Lake tables with ACID transactions

For access configuration details, see `/config/README.md`

---

**Last Updated**: November 2025  
**Project**: F1 Data Engineering Pipeline on Azure
