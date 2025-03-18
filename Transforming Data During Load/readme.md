
# Transforming Data During Load

Snowflake allows transformations while loading data using SQL functions in the `COPY INTO` command.

## Example - Loading with a SELECT Statement
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM (SELECT s.$1, s.$2 FROM @MANAGE_DB.external_stages.aws_stage s)
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES=('OrderDetails.csv');
```

## Example 1 - Creating and Loading a Table
```sql
CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS_EX (
    ORDER_ID VARCHAR(30),
    AMOUNT INT
);

SELECT * FROM OUR_FIRST_DB.PUBLIC.ORDERS_EX;
```

## Example 2 - Adding a Computed Column
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

## Example 3 - Extracting Substrings
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
