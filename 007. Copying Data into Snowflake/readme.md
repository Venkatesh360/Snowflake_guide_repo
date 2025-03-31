
# Copying Data into Snowflake

The `COPY INTO` command is used to load data into Snowflake tables from internal or external stages efficiently. It supports bulk data loading with various formatting options.

### Creating a Table for Data Loading
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

#### Checking Data in the Table
```sql
SELECT * FROM OUR_FIRST_DB.PUBLIC.ORDERS;
```

### Syntax
```sql
COPY INTO <table_name>
FROM <stage_location>
FILE_FORMAT = (TYPE = '<format>' [OPTIONS])
[ON_ERROR = '<action>']
```

### Key Parameters
- `<table_name>`: Target table where data will be loaded.
- `<stage_location>`: The stage containing the source data.
- `FILE_FORMAT`: Specifies the file format (e.g., CSV, JSON, PARQUET) and its properties.
- `ON_ERROR`: Defines error handling behavior (`ABORT_STATEMENT`, `CONTINUE`, `SKIP_FILE`).

### Example - Basic Copy Command
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    FILE_FORMAT = (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1);
```

### Example - Copy Command with Fully Qualified Stage Object
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

### Example - Copying Files Matching a Pattern
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    FILE_FORMAT= (TYPE = CSV FIELD_DELIMITER=',' SKIP_HEADER=1)
    PATTERN='.*Order.*';
```

For detailed options, refer to the [Snowflake Documentation](https://docs.snowflake.com/).

