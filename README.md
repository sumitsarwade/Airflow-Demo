# ETL Pipeline

## Overview

This ETL (Extract, Transform, Load) pipeline automates the process of downloading, processing, and loading web page view data into a SQL Server database using Apache Airflow. The pipeline consists of four main tasks, each performing a specific function in the ETL process.

## Pipeline Tasks

### 1. Get Data
- **Operator**: `BashOperator`
- **Description**: Downloads page view data for a specific hour.
- **Details**: 
  - Executes a `curl` command to fetch the data.
  - Utilizes the `execution_date` parameter to dynamically set the day, year, month, and hour.
  - Uses Jinja templated strings for parameter substitution.

### 2. Extract Gz
- **Operator**: `BashOperator`
- **Description**: Unzips the downloaded file.
- **Details**: 
  - Unzips the `.gz` file and stores the extracted data in a temporary directory for further processing.

### 3. Fetch Page Views
- **Operator**: `PythonOperator`
- **Description**: Processes the downloaded data to extract relevant columns and filter for specific companies.
- **Details**: 
  - Reads the unzipped data.
  - Extracts necessary columns and filters the data.
  - Generates an SQL script with `INSERT` statements for inserting the processed data into the SQL Server database.

### 4. Write to SQL Server
- **Operator**: `MsSqlOperator`
- **Description**: Loads the processed data into the SQL Server database.
- **Details**: 
  - Connects to the SQL Server database.
  - Executes the SQL script to insert the data.

## Concepts of Airflow Used

1. **Atomicity and Idempotency**
   - Each task in the pipeline is designed as an atomic unit of work.
   - The pipeline can be re-run without causing issues, ensuring idempotency.

2. **Runtime Parameters**
   - The `execution_date` parameter is used to dynamically fetch the data for the specified hour.

3. **Jinja Templated String**
   - Jinja templating is used to define the loading date and extract relevant information from the `execution_date`.

4. **Connecting to External Systems**
   - The pipeline demonstrates the ability to connect to an external system, specifically a SQL Server database, to load the transformed data.
