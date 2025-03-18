
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



