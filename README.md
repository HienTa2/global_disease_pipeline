# global_disease_pipeline

# Disease Analysis Workflow on Google Cloud Platform

This repository contains a complete ETL workflow for analyzing global disease data using Google Cloud Platform (GCP) and visualizing insights with Tableau Public. Below is a detailed overview of the project.

---

## Overview
The project aims to extract, transform, and analyze global disease data by leveraging GCP services, including Google Cloud Storage, Compute Engine (Apache Airflow), and BigQuery for data warehousing. Finally, the data is visualized on a Tableau dashboard.

---

## Architecture Diagram

![image](https://github.com/user-attachments/assets/624febec-cc10-4d50-bd8f-5f486b2ca1a6)


The workflow follows these steps:
1. **Global Data**: Data is uploaded to **Google Cloud Storage**.
2. **Compute Engine**: Apache Airflow orchestrates the ETL workflow.
3. **BigQuery Data Warehouse (DWH)**:
    - **Staging Dataset**: Raw data is loaded.
    - **Transform Dataset**: Data is cleaned and transformed.
    - **Reporting Dataset**: Optimized data is prepared for visualization.
4. **Visualization**: Data is visualized in a Tableau dashboard.

---

## Components
### 1. Google Cloud Platform Setup
- **Google Cloud Storage**:
  - Bucket: `bkt-src-global-data`
  - Contains raw CSV files (e.g., `global_health_data.csv`).
 
  - ![{B99C133C-0799-453C-ABBB-8CBF2DE2F60C}](https://github.com/user-attachments/assets/6035dac8-3516-4e45-bf84-2d1d59f3e69e)


- **Compute Engine**:
  - Virtual Machine: `hien-airflows` (used to run Apache Airflow).
  - Zone: `us-central1-f`.
 
    ![{2D5FCFB9-61D7-4106-B8E4-FE45D127EDA3}](https://github.com/user-attachments/assets/b3d50c0d-db89-4460-98b2-29238a9dbcac)


- **BigQuery DWH**:
  - **Staging Dataset**: Stores raw data (e.g., `global_data` table).
  - **Transform Dataset**: Holds cleaned and transformed data (e.g., `canada_table`, `france_table`, `germany_table`, etc.).
  - **Reporting Dataset**: Contains final data views for reporting (e.g., `canada_view`, `france_view`, `germany_view`, etc.).
 
  - ![{65B97DBE-BA17-47B1-8D6D-E2FEA691A770}](https://github.com/user-attachments/assets/22539df0-4bcc-4e54-b01e-b21ba68e42b1)


### 2. ETL Workflow
The ETL process is orchestrated using Apache Airflow with the following DAGs:

#### DAG Files:
- **00_gcs_bq.py**:
  - Extracts data from Cloud Storage and loads it into the Staging Dataset in BigQuery.

- **01_gsctobq-sensor.py**:
  - Ensures data availability in Cloud Storage before proceeding to BigQuery.

- **02_transformation.py**:
  - Transforms and cleans data in the Transform Dataset.

- **03_finalview.py**:
  - Creates final reporting views in the Reporting Dataset for Tableau.

#### DAG Execution Example:

![image](https://github.com/user-attachments/assets/30560840-c94b-4fca-aabc-3d49fb200177)


### 3. Tableau Visualization
- [Disease Analysis Dashboard](https://public.tableau.com/app/profile/hien.ta/viz/DiseaseAnalysisDashboard/DiseaseAnalysisDashboard?publish=yes)

The Tableau dashboard includes:
- **Prevalence vs. Incidence Scatterplot**: Compare diseases by their prevalence and incidence rates.
- **Category Breakdown Pie Chart**: Shows the proportion of diseases by category.
- **Trend Over Time Line Chart**: Displays prevalence trends over years.
- **Top 10 Diseases Bar Chart**: Highlights diseases with the highest prevalence rates.

---

## Source Data: `global_health_data.csv`

### Description
The `global_health_data.csv` file contains raw global health data that serves as the primary input for the ETL workflow. This dataset is based on the [Global Disease Data: Cases and Deaths (2020–2024)](https://www.kaggle.com/datasets/mustafahabeeb90/global-disease-data-cases-and-deaths-20202024?utm_source=chatgpt.com) dataset from Kaggle, adapted to include additional metrics for analysis.

### File Structure
| **Column Name**       | **Description**                               |
|------------------------|-----------------------------------------------|
| `year`                | Year of the data record                       |
| `country`             | Country name                                  |
| `disease_name`        | Name of the disease                           |
| `disease_category`    | Category of the disease (e.g., chronic, infectious) |
| `prevalence_rate`     | Prevalence rate of the disease                |
| `incidence_rate`      | Incidence rate of the disease                 |

### File Location
The file is uploaded to the Cloud Storage bucket:
- **Bucket Name**: `bkt-src-global-data`
- **File Path**: `global_health_data.csv`

---

## How to Run

### Prerequisites
- Google Cloud Platform project setup.
- Apache Airflow installed on Compute Engine.
- Tableau Public for data visualization.

### Steps
1. **Upload Data**:
   - Place your CSV file in the Cloud Storage bucket (`bkt-src-global-data`).

2. **Run ETL Workflow**:
   - SSH into the `hien-airflows` VM and trigger the DAGs in the following order:
     ```bash
     airflow dags trigger 00_gcs_bq
     airflow dags trigger 01_gsctobq-sensor
     airflow dags trigger 02_transformation
     airflow dags trigger 03_finalview
     ```

3. **Visualize Data**:
   - Use Tableau Public to connect to the final views in the Reporting Dataset or access the published [dashboard](https://public.tableau.com/app/profile/hien.ta/viz/DiseaseAnalysisDashboard/DiseaseAnalysisDashboard?publish=yes).

---

## Repository Structure
```
├── Dags
│   ├── 00_gcs_bq.py
│   ├── 01_gsctobq-sensor.py
│   ├── 02_transformation.py
│   ├── 03_finalview.py
├── dataset
├── README.md
```

---

## Future Enhancements
- Automate Tableau extracts for real-time updates.
- Add additional ETL validation checks.
- Expand visualization to include geospatial analysis.

---

## Author
**Hien Ta**

- [Tableau Profile](https://public.tableau.com/app/profile/hien.ta/viz/DiseaseAnalysisDashboard/DiseaseAnalysisDashboard?publish=yes)
