# Snowflake: Key Concepts & Architecture

## Overview
Snowflake is a self-managed data cloud platform that enables data storage, processing, and analytics with enhanced flexibility and performance compared to traditional systems. It is not built on existing database technologies but combines a new SQL engine with a cloud-native architecture.

## Data Platform as a Self-Managed Service
- No hardware or software installation required.
- Fully managed maintenance, upgrades, and tuning.
- Runs entirely on public cloud infrastructure.
- Uses virtual compute instances for processing and cloud storage for persistence.

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


