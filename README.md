# Superstore Final Project

## Overview

This repository contains the complete Superstore final project.

The main goal of this project was to transform the original Superstore dataset into a complete data analytics solution, starting from raw data preparation and data warehouse design, continuing with statistical analysis and machine learning models, and finishing with an interactive Power BI dashboard.

The project follows a complete data workflow:

* Data extraction and preparation
* Data warehouse construction
* Statistical investigation
* Machine learning modeling
* Business intelligence dashboard development

Each phase is organized separately with its own documentation, notebooks, and outputs.

## Tools We Used

* MySQL Server
* DataGrip
* Power BI Desktop
* Power Query
* DAX
* Python
* Pandas
* Scikit-learn
* Jupyter Notebook
* Microsoft Excel
* GitHub

## Project Structure

```text
Superstore Project
│
├── Phase 1 - Data preparation & warehouse
│   ├── Power BI data model
│   ├── MySQL data warehouse
│   ├── CSV dimension and fact tables
│   └── Data preparation scripts
│
├── Phase 2 - Statistical analysis
│   ├── Statistical tests
│   ├── Exploratory analysis
│   └── Research notebook
│
├── Phase 3 - Machine learning
│   ├── Part-1: Profit prediction model
│   └── Part-2: Ship mode classification model
│
└── Phase 4 - Power BI dashboard
    └── Final interactive dashboard
```

---

# Phase 1 — Data Preparation & Warehouse

## Overview

The first phase focused on cleaning the original Superstore dataset and creating a structured analytical database.

The raw data was imported into MySQL, processed through Power Query, validated, and transformed into a star schema suitable for analytics and machine learning.

## Work Completed

* Imported the original Superstore SQL database into MySQL.
* Connected Power BI to the database.
* Cleaned and validated source tables.
* Checked missing values, duplicate keys, and relationships.
* Created staging and final transformation layers.
* Built a star schema data model.
* Created fact and dimension tables.
* Added calendar information.
* Created QA validation measures.
* Exported final analytical tables.

## Final Data Model

The warehouse follows a star schema design.

### Fact Table

* `FactSales`

### Dimension Tables

* `DimProduct`
* `DimCustomer`
* `DimGeography`
* `DimShipMode`
* `DimOrderPriority`
* `DimDate`

The final fact table contains:

* 49,670 sales rows
* 25,033 unique orders

---

# Phase 2 — Statistical Analysis

## Overview

The second phase investigates business assumptions using statistical methods.

The main research question was:

**Does applying discounts lead to a statistically significant increase in quantity sold?**

The analysis was performed using the Superstore dataset prepared in Phase 1.

## Methodology

The workflow included:

* Data cleaning and validation
* Exploratory data analysis
* Distribution analysis
* Normality testing
* Variance testing
* Hypothesis testing
* Effect size analysis

Statistical methods included:

* Mann–Whitney U Test
* Welch's t-test
* Effect size calculations

The goal was not only to determine statistical significance but also to evaluate whether observed differences were practically meaningful.

---

# Phase 3 — Machine Learning

## Overview

The third phase applies machine learning techniques to predict business outcomes using historical sales information.

The prepared warehouse tables from Phase 1 were combined and transformed into machine learning datasets.

---

## Part 1 — Profit Prediction Model

### Goal

The objective was to predict product profit based on sales, customer, geographic, product, shipping, and order information.

## Process

The workflow included:

* Joining fact and dimension tables.
* Selecting useful features.
* Encoding categorical variables.
* Scaling numerical features.
* Splitting training and testing datasets.
* Training multiple regression models.
* Comparing model performance.

Models evaluated:

* Ridge Regression
* Random Forest Regression
* Gradient Boosting Regression

The final model comparison was based on:

* R² Score
* Training performance
* Testing performance
* Generalization ability

---

## Part 2 — Ship Mode Classification

### Goal

The objective was to predict the shipping mode of an order using historical order information.

A key focus of this project was preventing data leakage.

The dataset was aggregated to order level before splitting into training and validation sets, ensuring that items from the same order could not appear in both datasets.

## Process

The workflow included:

* Joining warehouse tables.
* Aggregating sales lines into orders.
* Creating order-level features.
* Engineering business-related variables.
* Training classification models.
* Evaluating performance.

Feature engineering included:

* Sales-related features
* Shipping cost features
* Profit margin features
* Discount features
* Time-based features

---

# Phase 4 — Power BI Dashboard

## Overview

The final phase presents the results through an interactive Power BI dashboard.

The dashboard combines the prepared warehouse model and analytical results into business-focused visualizations.

## Dashboard Goals

The dashboard provides insights into:

* Sales performance
* Profit analysis
* Product performance
* Customer behavior
* Geographic trends
* Shipping analysis
* Business KPIs

The dashboard uses the data model created in Phase 1 and provides an interactive layer for decision-making.

---

# How To Run The Project

## Data Warehouse

Navigate to:

```text
Phase 1 - Data preparation & warehouse
```

Run the generated SQL file:

```text
sql/Superstore_DataWarehouse.sql
```

This creates the complete analytical database.

---

## Statistical Analysis

Navigate to:

```text
Phase 2 - Statistical analysis
```

Open the notebook:

```text
Discount.ipynb
```

Run the notebook using Jupyter Notebook or Jupyter Lab.

---

## Machine Learning

Navigate to:

```text
Phase 3 - Machine learning
```

Install required Python packages:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy
```

Run the notebooks:

```text
Part-1/profit_prediction_model.ipynb

Part-2/Final_OrderLevel.ipynb
```

---

## Power BI Dashboard

Open:

```text
Phase 4 - Power BI dashboard
```

and run:

```text
Superstore_Phase4_Final_Report.pbix
```

using Power BI Desktop.

---

# Project Validation

The project was validated through multiple stages:

| Area                 | Validation                             |
| -------------------- | -------------------------------------- |
| Data Preparation     | Row counts, keys, relationships        |
| Data Warehouse       | Fact and dimension consistency         |
| Statistical Analysis | Assumption checks and hypothesis tests |
| Machine Learning     | Train/Test evaluation                  |
| Dashboard            | Business KPI verification              |

---

# Project Status

The complete Superstore analytics pipeline is currently finished.

Completed:

✅ Data warehouse creation
✅ Statistical analysis
✅ Machine learning models
✅ Power BI dashboard

The repository is ready for future improvements, additional analysis, and further business intelligence features.
