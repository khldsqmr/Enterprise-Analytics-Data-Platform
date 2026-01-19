# Enterprise Analytics Data Platform

This repository demonstrates the design and implementation of an enterprise-grade
analytics data platform using modern analytics engineering practices.

The project showcases how digital event data, operational metrics, and forecast
datasets can be unified across multiple cloud platforms using dbt, while enforcing
data quality, governance, and analytics-ready modeling standards.

---

## Objective

The objective of this project is to demonstrate how an enterprise analytics platform
can be built to support reliable reporting and decision-making by applying:

- Event-based data modeling
- Canonical (intermediate) data models
- Star schemas for analytics consumption
- Slowly Changing Dimensions (SCD Type 2)
- Bronze–Silver–Gold architecture
- Automated data quality validation using dbt
- CI-based quality gates for analytics workflows

This repository focuses on **analytics modeling and governance**, while intentionally
separating orchestration and infrastructure concerns.

---

## Data Sources (Simulated)

The platform integrates multiple enterprise-style data sources:

| Source | Platform | Schema Type |
|------|--------|-------------|
| Adobe digital events | BigQuery | Event-based, append-only |
| Media spend | ADLS / Databricks | Daily operational facts |
| Forecast data | Databricks | Time-series planning facts |
| Clickstream events | Pub/Sub | Streaming JSON events |

These datasets represent the **Bronze layer** and are treated as immutable inputs.

---

## Architecture Overview

The analytics platform follows a layered architecture:

### Bronze (Raw)
- Raw data ingested from cloud source systems
- No transformations or business logic applied
- Preserves source-of-record integrity

### Silver (Refined)
- Staging models for cleanup and standardization
- Canonical (intermediate) models to align semantics across platforms
- Implemented using dbt transformations

### Gold (Analytics)
- Consumer-ready fact and dimension tables
- Star schema design for analytics and BI tools
- SCD Type 2 dimensions for historical accuracy
- Optimized for reporting and downstream consumption

BigQuery serves as the **central analytics warehouse and source of truth**.


---

## Data Modeling Approach

### Event-Based Schemas
Digital interaction data is modeled using an append-only, event-based schema.
Each event represents a user interaction with associated context such as
timestamp, campaign, device, and event type.

### Canonical (Intermediate) Models
Canonical models align metrics and dimensions across heterogeneous source systems.
They provide a stable semantic layer that shields downstream analytics from
upstream source variability.

### Star Schema (Gold)
Analytics-ready datasets are modeled using star schemas:
- Fact tables capture measurable events and metrics
- Dimension tables provide descriptive context
- Optimized for BI, reporting, and ad hoc analysis

---

## Slowly Changing Dimensions (SCD Type 2)

Campaign dimensions are implemented using SCD Type 2 patterns to:
- Preserve historical attribute changes
- Maintain accurate point-in-time reporting
- Support incremental updates with effective dating

Each dimension record includes:
- Effective start and end dates
- A current record indicator

---

## Data Quality & Validation

Data quality is enforced using dbt tests at multiple layers:

- **Source tests**: enforce upstream data contracts
- **Model tests**: validate schema and metric correctness
- **Business logic tests**: ensure semantic consistency across datasets
- **SCD integrity tests**: prevent multiple active dimension records

Any test failure prevents analytics data from being promoted.

---

## CI/CD & Quality Gates

This project applies CI-based quality gates for analytics workflows:

- dbt tests are executed on pull requests
- Test failures block merges
- Broken transformations are prevented from reaching analytics-ready tables

CI is treated as a **data quality enforcement mechanism**, not a deployment tool.

---

## Orchestration & Execution (Conceptual)

Orchestration is intentionally kept outside this repository.

In a production environment, dbt models defined here would typically be executed
using:
- Kubernetes CronJobs
- Airflow / Cloud Composer
- dbt Cloud jobs

This repository focuses exclusively on **analytics logic, modeling, and governance**.

---

## Why This Project Matters

This project demonstrates:
- Enterprise-scale analytics modeling patterns
- Strong separation of concerns
- Data governance and quality enforcement
- Production-ready analytics engineering practices
- Multi-cloud awareness without unnecessary complexity

---

## Resume Summary (Backed by This Project)

Unified media spend, digital event, and forecast data across Databricks and BigQuery
using dbt, applying event-based schemas, canonical models, and star schemas with
SCD Type 2 dimensions for analytics-ready reporting.

---

## Repository Structure

Enterprise-Analytics-Data-Platform/
│
├── data_sources/ # Bronze: simulated cloud inputs
│ ├── bigquery/ # Event data (Adobe-style)
│ ├── adls_databricks/ # Spend and forecast data
│ └── event_streams/ # Streaming click events
│
├── enterprise_analytics_platform/ # Architecture & design documentation
│ ├── architecture/ # System design & data flow
│ ├── modeling/ # Schema & modeling patterns
│ └── governance/ # Data contracts & testing strategy
│
├── dbt/ # Analytics execution layer
│ ├── models/
│ │ ├── staging/ # Silver: 1:1 cleaned models
│ │ ├── intermediate/ # Silver: canonical models
│ │ └── marts/
│ │ ├── core/ # Gold: master facts & dimensions
│ │ └── analytics/ # Gold: aggregated metrics
│ ├── tests/ # Business logic & integrity tests
│ └── macros/
│
└── diagrams/ # Architecture & schema diagrams
