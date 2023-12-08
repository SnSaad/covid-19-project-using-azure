# COVID-19 Data Analysis with Microsoft Azure

## Overview

This comprehensive COVID-19 analysis project leverages Microsoft Azure services to perform seamless data ingestion, transformation, and loading into SQL databases. The project utilizes Azure tools for efficient data handling, employing Azure Data Factory for ingestion, Azure Databricks for data transformation, and Azure SQL Database for storage. Additionally, Power BI is integrated to visualize and analyze the transformed data, offering insightful dashboards and reports. This end-to-end solution provides a robust framework for studying and understanding COVID-19 trends, enabling users to gain valuable insights from the data. The project's GitHub repository contains detailed documentation, code, and configurations, facilitating easy replication and customization for diverse analytical purposes.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Setup](#setup)
- [Data Ingestion](#data-ingestion)
- [Data Transformation using Data Flow and Data Bricks](#data-transformation-using-data-flow-and-data-bricks)
- [Data Analysis](#data-analysis)
- [Results](#results)
- [Contributing](#contributing)

## Prerequisites

Before you get started, you will need the following:

- **Azure Data Factory (ADF)**: Ensure access to and familiarity with Azure Data Factory for orchestrating data workflows, including data ingestion from various sources into Azure services.
- **Azure Databricks (ADB)**: Familiarity with Azure Databricks is essential for data transformation tasks, as it provides a collaborative Apache Spark-based analytics platform for processing large-scale data.
- **Azure SQL Database (ASQL)**: Proficiency in setting up and managing Azure SQL Database, as it serves as the storage solution for processed data in this project.
- **Azure Subscription**: Access to an active Azure subscription is required to utilize the Azure services (ADF, ADB, ASQL) and deploy resources necessary for data management and analysis.
- **Power BI**: Basic understanding of Power BI for data visualization and reporting purposes, as the project integrates Power BI to create insightful dashboards and reports based on the transformed data from Azure services.

## Project Structure

The project is organized into the following sections:

- **Setup**: Step-by-step instructions for configuring your Azure environment and setting up necessary resources.
- **Data Ingestion**: Detailed guidance on acquiring COVID-19 data from the ECDC website and efficiently ingesting it into Azure Data Lake Storage Gen2 using Azure Data Factory.
- **Data Transformation**: Utilizing Azure Databricks for comprehensive data cleansing, transformation, and preparation, ensuring the data is refined for subsequent analysis.
- **Data Loading to Azure SQL**: Instructions for transferring the transformed data from Azure Data Lake or Azure Databricks to Azure SQL Database, facilitating easy access and retrieval of data.
- **Data Analysis using Power BI**: Demonstrating the process of fetching data from Azure SQL Database into Power BI for in-depth analysis, visualizations, and report generation.
- **Results**: A detailed overview of insights, trends, and findings derived from the data analysis using Power BI, showcasing the project's outcomes and conclusions.


## Setup

1. Create an Azure Account
2. Create an Azure Data Lake Storage Gen2 account.
3. Create an Azure Blob Storage account
4. Creat an Azure SQL Database
5. Set up an Azure Data Factory instance.
6. Configure an Azure Data Bricks workspace.
7. Prepare your development environment, including installing required libraries.

## Data Ingestion

In this section, we provide steps and scripts to ingest COVID-19 data into your Azure Data Lake Storage Gen2 account using Azure Data Factory. You can schedule data ingestion jobs to keep your data up to date.
To perform data ingestion from the European Centre for Disease Prevention and Control (ECDC) website to Azure Data Lake Storage Gen2 (ADLS Gen2), you can follow these steps:

![Screenshot (76)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/857ef53d-4519-43ba-966e-5d02223ed4fa)

### Step 1: JSON Configuration File
- Create a `config.json` file containing source URLs and sink file names for each dataset.
- Sample JSON structure:
    ```json
    [
        {
            "sourceBaseUrl": "https://example.com/hospital_data",
            "sourceRelativeUrl": "/hospital_data.json",
            "sinkFileName": "hospital_admission_data.json"
        },
        // Other dataset configurations follow the same format...
    ]
    ```
### Step 2: Azure Data Factory Setup
- Use Azure Data Factory to configure a Lookup activity to fetch dataset configurations from the `config.json` file.
- Define parameters to read source URLs and sink file names.

### Step 3: ForEach Activity for Data Copy
- Implement a ForEach activity in Azure Data Factory to iterate through each dataset listed in `config.json`.
- Use parameters retrieved from the Lookup activity to copy data from specified source URLs to Azure Data Lake Storage Gen2.

### Step 4: Data Storage in Data Lake Gen2
- Organize data into a 'raw' folder structure within Azure Data Lake Storage Gen2.
- Store datasets in respective 'raw' folders based on sink file names specified in `config.json`.

### Step 5: Pipeline Execution and Validation
- Run the Azure Data Factory pipeline to trigger data ingestion.
- Review execution logs to confirm successful ingestion for each dataset.
- Validate dataset presence in the designated 'raw' folders within Azure Data Lake Storage Gen2.

## Data Transformation using Data Flow and Data Bricks

Azure Data Flow is used to clean, transform, and prepare the data for analysis. We provide examples of data transformation tasks and best practices.

### Cases and Deaths Data

#### Overview
This section outlines the data transformation process for hospital admission data using Azure Data Flow within Azure Data Factory. The transformation steps include filtering data for European countries, pivoting columns, performing a lookup to acquire additional country codes, and finally storing the processed file in the Data Lake Storage Gen2 within the 'processed' folder.
![Screenshot (86)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/f435504b-d1af-410e-bbdd-c13522ac2814)
![Screenshot (84)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/86061ea9-c841-46a5-8075-b95264520bbc)

#### Transformation Steps
1. **Source Data**: Ingest hospital admission data from the specified data source.
2. **Filtering for European Countries**: Apply a filter to extract data exclusively for European countries from the dataset.
3. **Pivoting Columns**: Utilize Azure Data Flow to pivot columns, such as 'country' and additional relevant fields, for better analysis and visualization.
4. **Lookup Operation**: Perform a lookup operation to retrieve the two-digit and three-digit country codes to enrich the dataset.
5. **Sink Processed Data**: Store the transformed and enriched dataset into Data Lake Storage Gen2 within the 'processed' folder for subsequent analysis and reporting.

## Hospital Admission Dataset Transformation Process
![Screenshot (87)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/c749aadd-dbb4-4a9b-bb0e-ed5ad72f1eec)
![Screenshot (81)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/4dd5795b-5f97-40ae-be19-a465d80c8d16)

### Transformation Steps
1. **Source Data Extraction**: Retrieve the Hospital Admission dataset from the source.
2. **Conditional Splitting - Weekly and Daily Data**: Implement a conditional split to separate the data into weekly and daily records based on a defined condition. 
3. **Lookup Operation for Country Codes**: Utilize lookup activities to fetch country_2_digit_code and country_3_digit_code based on specific criteria for each record.
4. **Derived Column Creation - ecdc_year_week**: Create a derived column 'ecdc_year_week' using an expression combining year and week_of_year formatted as 'year-w-week_number'.
5. **Joining Weekly Datasets**: Merge individual weekly datasets into a single comprehensive dataset for further processing.
6. **Group By Operations**: Group the data by country, country_code_2_digit, country_code_3_digit, population, reported_date, week_start_date, week_end_date, and source.
7. **Derived Column - Matching Year-Weeks**: Create a derived column to validate if reported_year_week matches ecdc_year_week for data consistency.
8. **Pivoting Operation**: Perform pivoting on columns, such as 'country,' to restructure the dataset for easier analysis and reporting.
9. **Data Sinking**: Sink the transformed data into Azure Data Lake Storage within the 'processed' folder, separating daily and weekly datasets for storage and future usage.

#### How to Use
1. Open Azure Data Factory and navigate to the Azure Data Flow for cases_deaths data and hospital admission data transformation.
2. Configure the transformation steps as described above, specifying the source, filter, pivot, lookup, and sink operations.

### Data Transformation using Azure Databricks for Population Data
### Overview
This section details the process of data transformation using Azure Databricks for the Population dataset. The transformation involves mounting the data lake, filtering data to retain only the year 2019, grouping records by age using PySpark, and executing the transformation through a Databricks notebook activity.
![Screenshot (93)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/7a54679c-6ddb-4aa5-a7bb-31dc20ea1cd3)
![Screenshot (94)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/cd641d3a-98e7-449f-9202-e391e48e2dda)

### Transformation Steps

1. **Mounting Data Lake on Databricks**: Configure Azure Databricks to mount the data lake folder containing the population dataset, enabling access within the Databricks environment.
2. **Filtering Records for Year 2019**: Utilize PySpark to filter the population dataset, retaining data only for the year 2019 and removing records from other years.
3. **Grouping by Age**: Implement PySpark operations to group records based on age, aggregating data and creating structured groups for analysis.
4. **Databricks Notebook Activity**: Create a Databricks notebook containing the PySpark code for the aforementioned transformation steps.
5. **Execution of Transformation**: Execute the Databricks notebook activity within Azure Data Factory to run the PySpark code and perform the data transformation operations.

# Copy Data to Azure SQL Database
## Overview
This section delineates the process of creating an Azure SQL Database named 'covid_reporting' and its associated tables ('hospital admission', 'cases and deaths', 'testing', 'population'). Subsequently, it details the utilization of the Copy Data activity in Azure Data Factory to transfer data from the 'processed' folder in Azure Data Lake Storage to their respective tables in the Azure SQL Database.
![Screenshot (91)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/2fb94bb5-ce90-4abf-96d9-370f067569a5)
![Screenshot (96)](https://github.com/SnSaad/covid-19-project-using-azure/assets/98678581/bdcf79bb-ffec-4ca2-bcc5-b86dd880be3f)


### Steps
1. **Creating Azure SQL Database and Tables**:
   - Establish an Azure SQL Database named 'covid_reporting'.
   - Create tables within the database for 'hospital admission', 'cases and deaths', 'testing', and 'population' datasets.
2. **Configuring Copy Data Activity**:
   - Implement a Copy Data activity in Azure Data Factory to facilitate the data transfer process.
   - Configure source and sink settings within the Copy Data activity to extract data from the 'processed' folder in Azure Data Lake Storage and load it into the corresponding tables in the 'covid_reporting' Azure SQL Database.

# Power BI Data Fetch and Analysis

## Overview

This section describes the process of retrieving data from Azure SQL Database into Power BI for analysis. It outlines the configurations required to establish the connection between Power BI and Azure SQL, and highlights the analysis performed using Power BI.

### Steps
1. **Setting up Power BI Connection to Azure SQL**:
   - Configure Power BI to connect to Azure SQL Database.
   - Use Azure SQL configurations (server name, database name, credentials) to establish the connection in Power BI.
2. **Fetching Data into Power BI**:
   - Design queries or use Power BI's graphical interface to fetch data from Azure SQL Database tables into Power BI's data model.
3. **Data Analysis in Power BI**:
   - Perform various analyses such as:
     - Creating visualizations (charts, graphs, tables) based on fetched data.
     - Implementing filters, slicers, and other interactive elements to explore and analyze data trends.
     - Calculating key metrics, generating insights, and creating meaningful reports and dashboards.

---

**Disclaimer**: This project is for educational and demonstrative purposes only. It should not be used as a primary source for COVID-19 data. Always rely on official sources and follow recommended safety guidelines.
