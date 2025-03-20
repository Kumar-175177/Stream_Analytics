# Automated ETL Framework for Analytics on Azure

This repository contains an automated ETL framework that streamlines data processing and analytics using Azure services. The solution leverages Azure Data Factory for scalable, Spark-based ETL, Azure Functions for serverless orchestration, Azure Logic Apps for workflow management with built-in retries, and pushes processed data to both Azure Synapse Analytics and Azure Cognitive Search. This setup eliminates manual efforts, improves processing speeds, and provides real-time analytics capabilities.

## Table of Contents

- Overview
- Architecture & Data Flow
- Project Structure
- File Descriptions
- Setup & Prerequisites
- Usage
- Interview Explanation
- Learnings & Insights
- License

## Overview

This project demonstrates an end-to-end automated ETL framework that:

- Ingests and processes both structured and semi-structured data from Azure Blob Storage.
- Performs Spark-based transformations to compute key metrics like average TTI (Time To Interactive) and average TTAR (Time To Articulate Response) per page URL.
- Writes processed data to Azure Synapse Analytics for structured analytics and to Azure Cognitive Search for real-time search and visualization.
- Uses Azure Functions and Azure Logic Apps to orchestrate and automate periodic ETL tasks with built-in retry mechanisms and Azure Monitor logging for monitoring.
- This architecture optimizes query performance on large datasets via partitioning and indexing strategies in Synapse while enabling near-real-time analytics with Cognitive Search.

## Architecture & Data Flow

Below is an overview diagram of the ETL framework:

```
       +----------------+
       | Azure Blob Storage |
       |  (Data Sources)    |
       +-------+--------+
               │
               ▼
  +-----------------------------+
  |  Azure Data Factory (ADF)   |
  |  - Reads structured &       |
  |    semi-structured data     |
  |  - Applies Spark            |
  |    transformations          |
  |  - Computes average TTI,     |
  |    average TTAR, & counts    |
  |  - Writes to Synapse &       |
  |    Cognitive Search         |
  +--------------+--------------+
                 │
                 ▼
  +-----------------------------+
  |  Azure Synapse Analytics    |
  |  (Optimized for Analytics)  |
  +--------------+--------------+
                 │
                 ▼
  +-----------------------------+
  |   Azure Cognitive Search    |
  |  (Real-Time Analytics &     |
  |    Visualization)           |
  +--------------+--------------+
                 │
                 ▼
  +-----------------------------+
  | Azure Functions & Logic Apps|
  |  - Orchestrate ETL Workflow |
  |  - Automate retries with    |
  |    Azure Monitor logging    |
  +-----------------------------+
```

### Flow Summary

#### Data Ingestion & Transformation:

Azure Data Factory reads data from Blob Storage (both structured and semi-structured). The job applies Spark transformations, adds ingestion timestamps, filters out invalid records, and computes aggregated metrics (average TTI, average TTAR, and event counts per page URL).

#### Data Output:

The processed data is simultaneously written to Azure Synapse Analytics for structured analytics and to Azure Cognitive Search for real-time search and dashboarding.

#### Orchestration & Monitoring:

Azure Functions, triggered periodically (e.g., via Azure Event Grid or Logic Apps), initiates a Logic Apps workflow that starts the Data Factory pipeline. The workflow includes a retry mechanism with exponential backoff, and Azure Monitor captures detailed execution metrics and errors for monitoring and alerting.

## Project Structure

```
automated-etl-framework/
├── data_factory_etl_pipeline.py  # Azure Data Factory ETL pipeline to ingest, transform, and output data to Synapse & Cognitive Search
├── function_etl_trigger.py  # Azure Function to trigger the ETL workflow via Logic Apps
├── logic_apps_workflow.json  # Logic Apps workflow definition with retry mechanisms
├── README.md  # Project documentation (this file)
├── requirements.txt  # (Optional) Python dependencies for local testing
```

## File Descriptions

### `data_factory_etl_pipeline.py`

This script runs as an Azure Data Factory pipeline. It reads structured (Parquet) and semi-structured (JSON) data from Blob Storage, applies Spark-based transformations (adding ingestion timestamps, filtering, and aggregating metrics like average TTI and average TTAR per page URL), and writes the aggregated results to both Azure Synapse Analytics and Azure Cognitive Search.

### `function_etl_trigger.py`

This Azure Function triggers the ETL workflow by starting the Azure Logic Apps workflow. It passes the necessary parameters (such as Blob Storage input paths, Synapse connection details, and Cognitive Search endpoint/index) and relies on Azure Monitor for logging and error monitoring.

### `logic_apps_workflow.json`

This JSON file defines the Azure Logic Apps workflow. It orchestrates the Data Factory pipeline execution with a built-in retry mechanism (exponential backoff) to handle failures. Azure Monitor logging is integrated to enable monitoring and alerts.

## Setup & Prerequisites

- **Azure Data Factory:** Configure with necessary IAM roles and permissions to read from Blob Storage and write to Synapse/Cognitive Search.
- **Azure Blob Storage:** Container(s) containing structured and semi-structured data.
- **Azure Synapse Analytics:** Data warehouse with appropriate table design (partitioning, distribution, and sort keys) for optimized query performance.
- **Azure Cognitive Search:** Domain setup for real-time analytics and visualization.
- **Azure Functions:** Function configured with environment variables (e.g., Data Factory pipeline name, Logic Apps Workflow URL).
- **Azure Logic Apps:** Workflow configured to orchestrate the ETL workflow.
- **Azure Monitor:** For logging, monitoring, and setting up alerts on ETL failures or retries.

## Usage

1. **Deploy the Azure Data Factory Pipeline:** Upload `data_factory_etl_pipeline.py` to Azure Data Factory. Configure job parameters such as `input_path_structured`, `input_path_semi_structured`, `synapse_url`, `synapse_dbtable`, `synapse_temp_dir`, `cognitive_search_endpoint`, and `cognitive_search_index`.
2. **Deploy the Azure Function:** Upload `function_etl_trigger.py` to Azure Functions. Set the necessary environment variables and configure an Event Grid or Logic Apps trigger.
3. **Configure Azure Logic Apps:** Create a workflow using the definition in `logic_apps_workflow.json`. Ensure that the Logic Apps Workflow URL is updated in the Function App.
4. **Monitor and Alert:** Use Azure Monitor Logs and Alerts to monitor the execution of the ETL workflow and receive notifications in case of failures or retries.

## Interview Explanation

This project implements an automated ETL framework that efficiently processes both structured and semi-structured data using Azure Data Factory. The job ingests data from Blob Storage, applies Spark-based transformations to compute key metrics like average TTI and TTAR per page URL, and outputs the results to Azure Synapse Analytics for traditional analytics and to Azure Cognitive Search for real-time search and visualization. The process is fully automated and orchestrated by Azure Functions and Azure Logic Apps, which handle task retries with exponential backoff and integrate with Azure Monitor for detailed monitoring and alerting. This design eliminates manual intervention and ensures high scalability, reliability, and low latency in data processing.


 Project : Automated ETL Framework for Structured & Semi-Structured Data Processing

🔹 Business Context:
At Sony, we needed to efficiently process both structured (e.g., sales data) and semi-structured data (e.g., JSON web logs, clickstream data) to gain insights into product performance. The goal was to automate the ETL pipeline, ensuring low-latency data processing for both analytics and real-time search.

🔹 Key Challenges:
Processing large volumes of structured & semi-structured data efficiently.
Ensuring real-time searchability and traditional analytics within the same pipeline.
Implementing fault-tolerant, automated workflows with zero manual intervention.
Handling failures and retries effectively to ensure reliability.

🔹 Solution Approach:

1️⃣ Data Ingestion with Azure Data Factory (ADF)
Azure Blob Storage stores raw structured (CSV) and semi-structured (JSON) data.
ADF pipelines fetch data from Blob Storage and initiate Spark-based transformations.

2️⃣ Transformation with Apache Spark on Azure Synapse
Spark is used to compute key metrics like average TTI (Time to Interactive) and TTAR (Time to Action Response) per page URL.
Transformation logic is scalable to handle both real-time and batch processing.

3️⃣ Output Storage & Search Integration
Azure Synapse Analytics stores processed data for traditional analytics & BI reporting.
Azure Cognitive Search enables real-time search and visualization of key metrics.

4️⃣ Orchestration & Automation
Azure Functions & Logic Apps orchestrate the ETL pipeline, handling task retries with exponential backoff.
Azure Monitor is integrated for detailed tracking, logging, and alerting.


## Learnings & Insights

- **Automation & Scalability:** Leveraging managed Azure services (Data Factory, Functions, Logic Apps) enables scalable and automated ETL workflows.
- **Real-Time Analytics:** Pushing data to Cognitive Search allows for immediate insights and interactive dashboards.
- **Resiliency:** The built-in retry mechanism and Azure Monitor ensure the system is robust and fault-tolerant.
- **Optimized Data Processing:** Using Spark transformations in Data Factory enhances data processing speeds, while proper table design in Synapse improves query performance.

document i need to download


