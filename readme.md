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

## Loading Data in Snowflake

### Stages in Snowflake

Stages in Snowflake are storage locations used for loading and unloading data. They act as an intermediate layer between the source data and Snowflake tables.

#### Types of Stages

1. **Internal Stages**
   - Managed by Snowflake.
   - Stores data within Snowflake before loading it into tables.
   - Types:
     - **User Stage:** Automatically created for each user.
     - **Table Stage:** Automatically created for each table.
     - **Named Stage:** Manually created for organized storage.

2. **External Stages**
   - Reside in cloud storage services like AWS S3, Azure Blob Storage, or Google Cloud Storage.
   - Require integration with Snowflake using storage credentials.
   
#### Managing External Stages

To create and manage external stages, use the following SQL commands:

##### Creating an External Stage
```sql
CREATE OR REPLACE DATABASE MANAGE_DB;
CREATE OR REPLACE SCHEMA external_stages;

CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    URL='s3://bucketsnowflakes3'
    CREDENTIALS=(AWS_KEY_ID='ABCD_DUMMY_ID' AWS_SECRET_KEY='1234abcd_key');
```

##### Describing an External Stage
```sql
DESC STAGE MANAGE_DB.external_stages.aws_stage;
```

##### Altering an External Stage
```sql
ALTER STAGE aws_stage
    SET CREDENTIALS=(AWS_KEY_ID='XYZ_DUMMY_ID' AWS_SECRET_KEY='987xyz');
```

##### Creating a Publicly Accessible External Stage
```sql
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    URL='s3://bucketsnowflakes3';
```

##### Listing Files in a Stage
```sql
LIST @MANAGE_DB.external_stages.aws_stage;
```

### Copying Data into Snowflake

The `COPY INTO` command is used to load data into Snowflake tables from internal or external stages efficiently. It supports bulk data loading with various formatting options.

#### Creating a Table for Data Loading
```sql
USE DATABASE OUR_FIRST_DB;

CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT INT,
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));
```

##### Checking Data in the Table
```sql
SELECT * FROM OUR_FIRST_DB.PUBLIC.ORDERS;
```

#### Syntax
```sql
COPY INTO <table_name>
FROM <stage_location>
FILE_FORMAT = (TYPE = '<format>' [OPTIONS])
[ON_ERROR = '<action>']
```

#### Key Parameters
- `<table_name>`: Target table where data will be loaded.
- `<stage_location>`: The stage containing the source data.
- `FILE_FORMAT`: Specifies the file format (e.g., CSV, JSON, PARQUET) and its properties.
- `ON_ERROR`: Defines error handling behavior (`ABORT_STATEMENT`, `CONTINUE`, `SKIP_FILE`).

#### Example - Basic Copy Command
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    FILE_FORMAT = (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1);
```

#### Example - Copy Command with Fully Qualified Stage Object
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1);
```

#### Example - Copying Specific Files
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails.csv');
```

#### Example - Copying Files Matching a Pattern
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    PATTERN='.*Order.*';
```

For detailed options, refer to the [Snowflake Documentation](https://docs.snowflake.com/).


## Transforming Data During Load

Snowflake allows transformations while loading data using SQL functions in the `COPY INTO` command.

### Example - Loading with a SELECT Statement
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM (SELECT s.$1, s.$2 FROM @MANAGE_DB.external_stages.aws_stage s)
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES=('OrderDetails.csv');
```

### Example 1 - Creating and Loading a Table
```sql
CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS_EX (
    ORDER_ID VARCHAR(30),
    AMOUNT INT
);

SELECT * FROM OUR_FIRST_DB.PUBLIC.ORDERS_EX;
```

### Example 2 - Adding a Computed Column
```sql
CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS_EX (
    ORDER_ID VARCHAR(30),
    AMOUNT INT,
    PROFIT INT,
    PROFITABLE_FLAG VARCHAR(30)
);

COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM (SELECT 
            s.$1,
            s.$2, 
            s.$3,
            CASE WHEN CAST(s.$3 AS INT) < 0 THEN 'not profitable' ELSE 'profitable' END 
          FROM @MANAGE_DB.external_stages.aws_stage s)
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES=('OrderDetails.csv');

SELECT * FROM OUR_FIRST_DB.PUBLIC.ORDERS_EX;
```

### Example 3 - Extracting Substrings
```sql
CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS_EX (
    ORDER_ID VARCHAR(30),
    AMOUNT INT,
    PROFIT INT,
    CATEGORY_SUBSTRING VARCHAR(5)
);

COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM (SELECT 
            s.$1,
            s.$2, 
            s.$3,
            SUBSTRING(s.$5,1,5) 
          FROM @MANAGE_DB.external_stages.aws_stage s)
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES=('OrderDetails.csv');

SELECT * FROM OUR_FIRST_DB.PUBLIC.ORDERS_EX;
```
## Error Handling in Snowflake Data Load

### Creating a New Stage
```sql
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage_errorex
    URL='s3://bucketsnowflakes4';
```

### Listing Files in the Stage
```sql
LIST @MANAGE_DB.external_stages.aws_stage_errorex;
```

### Creating an Example Table
```sql
CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS_EX (
    ORDER_ID VARCHAR(30),
    AMOUNT INT,
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));
```

### Handling Errors with ON_ERROR Options
#### Default Error Handling (Abort on Error)
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails_error.csv');
```

#### ON_ERROR = CONTINUE
The `ON_ERROR = 'CONTINUE'` option in Snowflake allows loading data while skipping problematic records instead of failing the entire operation.

```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails_error.csv')
    ON_ERROR = 'CONTINUE';
```
### Validating Skipped Records

After loading data with `ON_ERROR = CONTINUE`, you can use the `VALIDATE()` function to retrieve records that were skipped due to errors.

```sql
SELECT * FROM TABLE(VALIDATE(ORDERS_EX, JOB_ID => '_last'));
```

##### Explanation of `VALIDATE()` Arguments
- **Arg 1:** The target table where data was loaded (`ORDERS_EX`).  
- **Arg 2:** The query ID of the `COPY INTO` operation. You can use `'_last'` to reference the most recent `COPY INTO` execution.

This approach helps in identifying and troubleshooting problematic records without stopping the entire data load process.

#### ON_ERROR = SKIP_FILE
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails_error.csv','OrderDetails_error2.csv')
    ON_ERROR = 'SKIP_FILE';
```

#### ON_ERROR = SKIP_FILE_<number>
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails_error.csv','OrderDetails_error2.csv')
    ON_ERROR = 'SKIP_FILE_3';
```

### Creating and Using File Format Objects
```sql
CREATE OR REPLACE SCHEMA MANAGE_DB.file_formats;
CREATE OR REPLACE FILE FORMAT MANAGE_DB.file_formats.my_file_format;
DESC FILE FORMAT MANAGE_DB.file_formats.my_file_format;
```

#### Using a File Format Object in COPY Command
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (FORMAT_NAME=MANAGE_DB.file_formats.my_file_format)
    FILES = ('OrderDetails_error.csv')
    ON_ERROR = 'SKIP_FILE_3';
```

For detailed options, refer to the [Snowflake Documentation](https://docs.snowflake.com/).

# Data Loading with VALIDATION_MODE in Snowflake

Using `VALIDATION_MODE` to verify data integrity before loading into Snowflake.

## Preparing the Database and Table

Before loading data, create the necessary database and table:

```sql
CREATE OR REPLACE DATABASE COPY_DB;

CREATE OR REPLACE TABLE COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30)
);
```

## Setting Up the Stage

To load data from an external source like AWS S3, create a Snowflake stage:

```sql
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    URL='s3://snowflakebucket-copyoption/size/';
```

List the files in the stage:

```sql
LIST @COPY_DB.PUBLIC.aws_stage_copy;
```

## Using VALIDATION_MODE in COPY INTO

### Returning Errors
To check for errors before fully loading data, use:

```sql
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    FILE_FORMAT = (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    PATTERN='.*Order.*'
    VALIDATION_MODE = RETURN_ERRORS;
```

### Previewing Data with RETURN_5_ROWS
To inspect a sample of data before loading, use:

```sql
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    FILE_FORMAT = (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    PATTERN='.*Order.*'
    VALIDATION_MODE = RETURN_5_ROWS;
```

## Handling Files with Errors

To validate problematic files before attempting a full load, define a separate stage:

```sql
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    URL='s3://snowflakebucket-copyoption/returnfailed/';
```

List the staged files:

```sql
LIST @COPY_DB.PUBLIC.aws_stage_copy;
```

Retrieve all errors with:

```sql
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @COPY_DB.PUBLIC.aws_stage_copy
    FILE_FORMAT = (TYPE=CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    PATTERN='.*Order.*'
    VALIDATION_MODE = RETURN_ERRORS;
```

## Validating a Specific Number of Rows

To check only a few rows, use:

```sql
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @COPY_DB.PUBLIC.aws_stage_copy
    FILE_FORMAT = (TYPE=CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    PATTERN='.*error.*'
    VALIDATION_MODE = RETURN_1_ROWS;
```

## Conclusion

Using `VALIDATION_MODE` in Snowflake's `COPY INTO` command ensures data quality by previewing, validating errors, and testing small data samples before full ingestion.


