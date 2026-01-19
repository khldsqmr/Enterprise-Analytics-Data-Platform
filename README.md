# Enterprise Analytics Data Platform

This repository contains a sample enterprise analytics data platform built
to demonstrate how analytics data is modeled, governed, and validated
in real-world environments.

The focus of this project is on **analytics engineering**, not orchestration
or infrastructure. It shows how data from multiple platforms can be shaped
into analytics-ready tables using dbt with clear layering, testing, and
modeling standards.

---

## What this project shows

This project demonstrates:

- How to model event data, operational metrics, and forecasts together
- How Bronze, Silver, and Gold layers are applied in practice
- How canonical (intermediate) models reduce source-system complexity
- How star schemas are built for analytics and reporting
- How Slowly Changing Dimensions (Type 2) are handled
- How data quality is enforced using dbt tests
- How CI can be used as a quality gate for analytics code

The goal is not to build a full production system, but to clearly show
how these patterns are applied.

---

## Data sources (simulated)

The platform works with multiple types of data, similar to what you would
see in a real analytics environment:

| Source | Platform | Notes |
|------|--------|------|
| Digital events | BigQuery | Event-based (Adobe-style) |
| Media spend | ADLS / Databricks | Daily spend and performance |
| Forecasts | Databricks | Weekly planning data |
| Clickstream events | Pub/Sub | Streaming-style JSON |

These datasets represent the **raw (Bronze) layer** and are treated as
immutable inputs.

---

## Architecture overview

The data is organized using a standard Bronze–Silver–Gold pattern.

### Bronze
- Raw data from source systems
- No transformations or business logic
- Serves as the system of record

### Silver
- Staging models for cleaning and standardization
- Canonical (intermediate) models that align fields and definitions
- Implemented using dbt

### Gold
- Analytics-ready fact and dimension tables
- Star schema design
- Tables intended for BI tools and reporting

BigQuery acts as the central analytics warehouse.

---

## Modeling approach

### Event data
User interactions are modeled using an append-only event schema.
Each row represents a single interaction with a timestamp and context
such as campaign, page, or device.

### Canonical models
Intermediate models sit between raw sources and analytics tables.
They normalize naming, data types, and meanings so downstream models
do not depend on individual source quirks.

### Star schema
Final analytics tables follow a star schema pattern:
- Fact tables for events, spend, and forecasts
- Dimension tables for descriptive attributes

This makes reporting simpler and more consistent.

---

## Slowly Changing Dimensions (Type 2)

Campaign attributes are modeled using SCD Type 2 logic.
This allows historical changes to be preserved and supports
accurate point-in-time reporting.

Each dimension record includes:
- Effective start date
- Effective end date
- A current-record flag

---

## Data quality and testing

Data quality is enforced using dbt tests at multiple levels:

- Source-level tests to catch bad upstream data
- Model-level tests for schema and metric correctness
- Business logic tests to ensure consistency across datasets
- Integrity checks for SCD dimensions

If a test fails, the pipeline is considered broken.

---

## CI and quality gates

This project uses CI to run dbt tests on pull requests.
Any failing test blocks the merge.

CI is used strictly as a **data quality gate**, not as a deployment mechanism.

---

## Orchestration (out of scope)

Job scheduling and execution are intentionally not included here.

In a production setup, dbt models like these would typically be executed
using Airflow, Kubernetes jobs, or dbt Cloud. This repository focuses
only on modeling and governance.

---

## Why this repo exists

This project is meant to reflect how analytics platforms are actually
built and maintained:

- Clear layering
- Strong modeling discipline
- Explicit data quality checks
- Separation of concerns

It is designed to be readable, extendable, and realistic.
