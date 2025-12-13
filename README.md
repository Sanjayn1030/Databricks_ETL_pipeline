# TITLE :  Data Engineering Project: End-to-End ETL Pipeline with Databricks, AWS S3, and BI Dashboarding

A complete consolidation of Atlon and SportsBar sales data using a Medallion Architecture (Bronze–Silver–Gold), automated ETL pipelines, and a unified Sales Insights dashboard.

## Data Engineering Project – Atlon & SportsBar Consolidation
#### Overview

This project builds a complete data engineering pipeline using Databricks Free Edition. It brings together two very different data ecosystems:

##### Atlon: a mature sports equipment manufacturer with structured ERP exports

##### SportsBar: a fast-growing nutrition startup with scattered, inconsistent, messy data

The main objective was to build one reliable analytics layer, automate batch and incremental loads, and publish a clean BI dashboard that gives stakeholders an accurate view of consolidated sales performance.

### Why This Project Matters

SportsBar’s data arrived in every format possible: spreadsheets, WhatsApp dumps, inconsistent dates, missing values, and mismatched product IDs. Atlon had clean ERP exports but used a different structure altogether.

To make these two worlds talk to each other, I built:

A unified Medallion Architecture

A robust ETL pipeline (batch + incremental)

Cleaned, standardized, and merged datasets

A consolidated Gold layer for analytics

A BI dashboard that presents insights clearly

### What the Project Includes
### 1. Medallion Architecture (Bronze → Silver → Gold)

I used Databricks to structure and manage the pipeline:

#### Bronze: Raw ingestion from S3 with metadata and audit tracking

#### Silver: Deduplication, null handling, date cleanup, spelling corrections, valid surrogate key generation

#### Gold: Aggregations, business rules, monthly roll-ups, and parent–child dataset merges

### Data Model Overview

The project uses a classic star schema in the Gold layer:

#### Dimensions

**dim_customers** – customer details, market, platform, channel

**dim_products** – division, category, product, variant

**dim_gross_price** – price per product per year

**dim_date** – scripted date table for time-series reporting

### Fact

**fact_orders** – monthly aggregated sold quantity for both companies

The child company (SportsBar) provides daily data, which is rolled up to monthly granularity to match Atlon’s schema.

### How the Pipeline Works
**Customer Processing**

Cleaned spelling issues and whitespace

Standardized casing

Fixed invalid and missing cities

Normalized IDs

Added business metadata like market and channel

Merged into the parent customer table

**Product Processing**

Removed duplicates

Corrected spelling inconsistencies with regex

Derived division values

Extracted product variants

Built a reliable SHA-based product code

Upserted into the parent product table

**Gross Price Processing**

Cleaned irregular date formats

Fixed negative and “unknown” pricing

Joined to products to pull in the surrogate product code

Aggregated monthly → latest price per year

Updated parent pricing table

#### Fact Orders (Full + Incremental Load)

**Full Load (5 months)**

Ingested all landing files at once

Cleaned date formats

Removed duplicates

Joined to product surrogate keys

Aggregated daily → monthly

Upserted into parent fact table

**Incremental Load (Daily updates)**

Processed only new daily files using staging tables

Applied the same cleaning rules

Updated child daily fact table

Recalculated the monthly roll-up for the parent table

### Pipeline Orchestration

I automated the entire workflow with Databricks Jobs:

Task order:

Customers

Products

Gross Price

Fact Orders (Incremental)

The job is scheduled to run daily and can easily scale as more data arrives.

### Analytics Layer (Denormalized View)

Before building the dashboard, I created a single denormalized view by joining:

fact_orders

dim_customers

dim_products

dim_gross_price

dim_date

This view includes calculated fields like total_revenue and allows BI tools to query a single unified dataset.

### Sales Insights Dashboard

I exported the dashboard JSON and included it in the repo.
The dashboard covers:

Total Revenue

Total Quantity

Unique Customers

Average Selling Price

Number of Active Products

### Charts include:

Top 10 Products by Revenue

Revenue share by channel

Price vs Quantity scatter

Monthly revenue trend

Category performance over time

Everything is filterable by Year, Quarter, Month, Channel, and Category.

### How to Use This Repository

Clone the repo

Import notebooks into Databricks (or connect via Databricks Repos)

Configure the S3 connection

Run the pipeline notebooks in the recommended order

Import the dashboard JSON file into Databricks

Use the final denormalized view for reporting

### What I Learned

This project pulled together multiple real-world concepts:

Handling messy, inconsistent startup data

Designing scalable ETL workflows

Creating surrogate keys when the source data is unreliable

Managing batch + incremental loads

Using Databricks Jobs for orchestration

Building BI dashboards inside Databricks

Structuring a project for readability and long-term maintainability

### Final Outcome

The project delivers:

A clean and reliable consolidated dataset

A scalable long-term pipeline

Automated processing

A business-ready dashboard with meaningful KPIs

It meets the three goals set at the start:
1)  unified insights
2) low learning curve
3) scalable architecture
