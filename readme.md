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


## Warehouses in Snowflake
Warehouses are required for query execution and DML operations, including data loading. They can be dynamically resized and automatically started or suspended.

### Warehouse Types
#### Standard Warehouses
- Used for general SQL workloads.
- Support resizing, auto-suspend, and auto-resume.
- Suitable for transactional and analytical processing.

#### Snowpark Warehouses
- Optimized for Python-based Snowpark workloads.
- Ensure efficient execution of machine learning and AI models.
- Provide separate compute resources for Python execution to optimize cost and performance.

### Multi-Cluster Warehouses
Multi-cluster warehouses automatically scale based on query concurrency. They are useful for high-demand scenarios, such as large-scale reporting or real-time analytics.

- **Min and Max Clusters:** Define the range of clusters that can be active.
- **Scaling Policy:**
  - **Standard:** Adds clusters only when queries are queued.
  - **Economy:** Adds clusters more conservatively to optimize cost.
- **Auto-Suspension:** Multi-cluster warehouses suspend only when the minimum cluster count is reached.

### Warehouse Size & Credit Usage
| Warehouse Size | Credits/Hour | Notes |
|---------------|-------------|-------|
| X-Small      | 1           | Default for Snowsight & CREATE WAREHOUSE |
| Small        | 2           | |
| Medium       | 4           | |
| Large        | 8           | |
| X-Large      | 16          | Default for Classic Console |
| 2X-Large     | 32          | |
| 3X-Large     | 64          | |
| 4X-Large     | 128         | |
| 5X-Large     | 256         | Available in AWS & Azure, preview in US Gov regions |
| 6X-Large     | 512         | Available in AWS & Azure, preview in US Gov regions |

### Auto-Suspension & Auto-Resumption
- **Auto-Suspend:** Suspends inactive warehouses to save credits.
- **Auto-Resume:** Restarts warehouses when queries are submitted.

### Query Processing & Concurrency
- Query performance scales with warehouse size.
- Large queries benefit from larger warehouses, but small queries may not.
- Queries exceeding resource limits are queued until resources are available.
- Multi-cluster warehouses enable automated scaling for concurrency.

### Warehouse Usage in Sessions
- A session does not have a default warehouse unless explicitly set.
- Users can have a **default warehouse** assigned for automatic usage.
- Clients (SnowSQL, JDBC, ODBC) can specify default warehouses.

### Cost Management Recommendations
- Use **SYSTEM$STREAMLIT_NOTEBOOK_WH** for Streamlit and Notebook workloads.
- Separate Python workloads from SQL queries to reduce costs.
- Default warehouse precedence: User setting → Client configuration → Command-line parameter.

For more details, refer to the [Snowflake Documentation](https://docs.snowflake.com/).


## Scaling Policy in Snowflake

Scaling policies in Snowflake determine how virtual warehouses add or remove compute clusters based on workload demand. This feature is particularly useful for multi-cluster warehouses to optimize cost and performance.

### Scaling Policies
Snowflake provides two primary scaling policies:

#### 1. Standard Scaling
- Clusters are added **only when queries are queued** due to insufficient compute resources.
- Ensures rapid response to demand but may cause slight delays while clusters start.
- Best suited for workloads with fluctuating demand but strict performance requirements.

#### 2. Economy Scaling
- Clusters are added **more conservatively** to minimize credit consumption.
- Prioritizes cost savings over immediate performance.
- Ideal for workloads where slight delays are acceptable to reduce costs.

### Multi-Cluster Warehouse Scaling
When using a **multi-cluster warehouse**, the following parameters affect scaling behavior:
- **Min and Max Clusters:** Defines the range of clusters available for scaling.
- **Auto-Suspension:** Active clusters suspend individually, but only down to the minimum set cluster count.
- **Auto-Resume:** Additional clusters are brought online as query load increases.

### Choosing the Right Scaling Policy
- Use **Standard Scaling** when performance and low query latency are priorities.
- Use **Economy Scaling** for predictable workloads where cost savings outweigh performance considerations.

## Roles in Snowflake

Roles in Snowflake define user permissions and control access to objects such as databases, warehouses, and schemas. Snowflake follows a **role-based access control (RBAC)** model, where users are assigned roles, and roles are granted specific privileges.

### Default System Roles
Snowflake provides several predefined roles with varying levels of access:

- **ACCOUNTADMIN**  
  - The highest-level role with full control over the Snowflake account.  
  - Can manage users, roles, warehouses, security policies, and billing.  
  - Recommended for a limited number of users due to its extensive privileges.

- **SYSADMIN**  
  - Has full control over databases, schemas, and warehouses but does not manage users or security.  
  - Can create and modify objects within the account.  
  - Typically assigned to data engineers or database administrators.

- **SECURITYADMIN**  
  - Manages user accounts, role grants, and security-related configurations.  
  - Can create and manage roles but does not have full control over databases.  
  - Essential for managing role-based security.

- **USERADMIN**  
  - Responsible for creating, modifying, and deleting users and roles.  
  - Does not have access to databases or warehouses.  
  - Typically assigned to administrators managing user accounts.

- **PUBLIC**  
  - The default role assigned to all users.  
  - Has minimal privileges, typically used for viewing public objects.

### Custom Roles
Organizations can define custom roles to fit their specific needs:

- **Data Engineer Role**  
  - Can create and manage schemas, tables, and warehouses.  
  - Requires `CREATE DATABASE`, `CREATE SCHEMA`, and warehouse-related privileges.

- **Data Analyst Role**  
  - Read-only access to specific schemas and tables.  
  - Requires `SELECT` privileges but not modification rights.

- **Application Role**  
  - Assigned to external applications accessing Snowflake via APIs.  
  - Requires specific privileges on required objects without exposing unnecessary data.

### Role Hierarchy & Best Practices
- Snowflake allows roles to be **granted to other roles**, forming a hierarchy.
- The **ACCOUNTADMIN** role has access to all other roles.
- **Best Practices:**
  - Follow the **principle of least privilege**—grant only the necessary permissions.
  - Use **separate roles for different responsibilities** (e.g., `DATA_INGESTION`, `REPORTING`).
  - Regularly **review and audit role assignments** to ensure compliance.

