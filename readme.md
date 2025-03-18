# Snowflake: Key Concepts & Architecture

## Overview
Snowflake is a self-managed data cloud platform that enables data storage, processing, and analytics with enhanced flexibility and performance compared to traditional systems. It is not built on existing database technologies but combines a new SQL engine with a cloud-native architecture.

## Data Platform as a Self-Managed Service
- No hardware or software installation required.
- Fully managed maintenance, upgrades, and tuning.
- Runs entirely on public cloud infrastructure.
- Uses virtual compute instances for processing and cloud storage for persistence.

## Snowflake Architecture
Snowflake integrates aspects of both shared-disk and shared-nothing architectures:
- **Shared-disk:** Centralized data repository accessible by all compute nodes.
- **Shared-nothing:** Massively parallel processing (MPP) with distributed compute clusters.
- Provides both the simplicity of shared-disk and the scalability of shared-nothing architectures.

### Three Key Layers of Architecture
#### 1. Database Storage
- Data is optimized, compressed, and stored in Snowflake’s internal format.
- Fully managed storage handling file organization, compression, metadata, and statistics.
- Data access is only available through SQL queries.

#### 2. Query Processing
- Queries are executed via **virtual warehouses**, which are independent MPP clusters.
- Each virtual warehouse operates separately, ensuring no performance impact on others.

#### 3. Cloud Services
Manages overall coordination within Snowflake, including:
- Authentication & Access Control
- Infrastructure & Metadata Management
- Query Parsing & Optimization

## Connecting to Snowflake
Snowflake supports multiple connection methods:
- **Web UI:** Complete access to Snowflake’s management and usage.
- **Command Line Clients (SnowSQL):** Full management access via CLI.
- **ODBC & JDBC Drivers:** Connect third-party applications like Tableau.
- **Native Connectors:** Support for Python, Spark, etc.
- **Third-Party Integrations:** ETL and BI tools such as Informatica and ThoughtSpot.

