
# Handling Errors with ON_ERROR Options
## Default Error Handling (Abort on Error)
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails_error.csv');
```

## ON_ERROR = CONTINUE
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

#### Explanation of `VALIDATE()` Arguments
- **Arg 1:** The target table where data was loaded (`ORDERS_EX`).  
- **Arg 2:** The query ID of the `COPY INTO` operation. You can use `'_last'` to reference the most recent `COPY INTO` execution.

This approach helps in identifying and troubleshooting problematic records without stopping the entire data load process.

## ON_ERROR = SKIP_FILE
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    FILES = ('OrderDetails_error.csv','OrderDetails_error2.csv')
    ON_ERROR = 'SKIP_FILE';
```

## ON_ERROR = SKIP_FILE_<number>
```sql