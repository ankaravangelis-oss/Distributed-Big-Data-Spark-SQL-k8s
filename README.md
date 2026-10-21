# Distributed Big Data Analytics on Los Angeles Open Data (Apache Spark & Kubernetes)

## Overview
This repository contains a distributed big data analytics pipeline developed during the M.Sc. in Data Science and Machine Learning at the National Technical University of Athens (NTUA). The project leverages **Apache Spark (>=3.5)** running on a distributed **Kubernetes** cluster with **Hadoop HDFS** to process  datasets from the City of Los Angeles 

The main focus is on distributed query engineering, execution plan optimization ( Spark's Catalyst Optimizer), and format benchmarking (CSV vs. Apache Parquet).

---

## Datasets
The datasets needed for the project are the following: 
- **Los Angeles Crime Data (2010–2025):** Crime records of around 15 years containing temporal data, crime descriptions, and spatial coordinates.
- **2020 LA County Census Blocks:** Geographic and demographic population data in GeoJSON and CSV formats.
- **Median Household Income by Zip Code (2021):** Economic data across LA county zip codes.
- **LAPD Police Stations:** Geospatial coordinates of the 21 active police division headquarters in Los Angeles.



## Analytical Queries & Implementation Objectives

### 1. Storage & I/O Benchmark: CSV vs. Parquet (`convert_to_parquet.py`, `query1_parquet.py`)
- **Objective:** Evaluated I/O throughput by converting raw CSV data into columnar Apache Parquet.
- **Benchmark:** Compared query execution latency between CSV and Parquet files.

### 2. Query 1: Crime Distribution on "Street" Incidents (`query1.py`)
- **Objective:** Categorized and ranked daily time segments (Morning, Afternoon, Evening, Night) based on the percentage of crimes taking place only on the streets (`"STREET"`).
- **APIs Evaluated:**  **DataFrame API** (with and without UDFs) and low-level **RDD API**. It was executed with 2 worker executors to benchmark the differences.

### 3. Query 2: Dedermination of the Peak Crime Period  (`query2.py`)
- **Objective:** Extracted the top 3 peak crime months per year, computing total incident volume and relative annual ranking over the 15-year period.
- **APIs Evaluated:**  **DataFrame API** vs **Spark SQL API** using 4 worker executors.

### 4. Query 3: Per-Capita Income Analysis via Multi-Source Joins (`query3.py`)
- **Objective:** Integrated 2020 demographic data with 2021 household income to calculate the average annual per-capita income per LA Zip Code.
- **APIs Evaluated:** Both **DataFrame** and **RDD** on a 3-executor cluster.

### 5. Query 4: Geospatial Proximity to Police Headquarters (`query4.py`)
- **Objective:** Identified the nearest LAPD police division for each incident using spatial distance calculations, aggregating total incident counts and average distance per division. It is important to be mentioned that not every crime's closest Police Division should be in the same division. 
- **Catalyst Optimizer Exploration:** Evaluated distributed join execution plans using `explain()` and execution hints (`BROADCAST`, `MERGE`, `SHUFFLE_HASH`, `SHUFFLE_REPLICATE_NL`).
- **Scalability Benchmarking:** 
  - *Vertical Scaling:* Evaluated performance scaling from 1 core/2GB to 4 cores/8GB per executor.
  - *Horizontal Scaling:* Scaled cluster execution maintaining 8 cores & 16GB RAM across varying executor counts (2, 4, and 8 executors).
