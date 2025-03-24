# Comprehensive Guide to Copying Data into Snowflake

This guide covers all aspects of loading data into Snowflake using the COPY INTO command, including CSV, JSON, Parquet, and best practices.

## 1. Methods for Loading Data into Snowflake

| Method | Best For |
|--------|---------|
| COPY INTO (from Stage) | Best for bulk loading from cloud storage (S3, GCS, Azure). |
| PUT + COPY INTO (Internal Stage) | Load files from local disk into Snowflake internal staging. |
| Snowflake Web UI (Snowsight) | Simple UI-based upload (for small CSV/JSON files). |
| SnowSQL CLI | Command-line bulk loading. |
| Python (Snowpark, Pandas) | Programmatic data loading for ETL pipelines. |
| External Tables | Query cloud storage files without importing data. |

## 2. Using COPY INTO to Load Data

The COPY INTO command is the most efficient way to bulk load data into Snowflake.

### Step 1: Create a Table

```sql
CREATE OR REPLACE TABLE employees (
    id INT,
    name STRING,
    department STRING,
    salary FLOAT
);
```

### Step 2: Choose a Staging Method

#### A. Load from an Internal Stage

1. Create an Internal Stage

```sql
CREATE OR REPLACE STAGE my_internal_stage;
```

2. Upload File to Internal Stage (Using SnowSQL CLI)

```sh
PUT file://path_to_your_file/employees.csv @my_internal_stage;
```

#### B. Load from an External Stage (AWS S3, GCS, or Azure)

1. Create an External Stage

```sql
CREATE OR REPLACE STAGE my_s3_stage
URL = 's3://my-bucket/path/'
CREDENTIALS = (AWS_KEY_ID='your_key' AWS_SECRET_KEY='your_secret');
```

2. Verify Files in the External Stage

```sql
LIST @my_s3_stage;
```

### Step 3: Load Data into the Table

#### Load from Internal Stage

```sql
COPY INTO employees
FROM @my_internal_stage/employees.csv
FILE_FORMAT = (TYPE = 'CSV' SKIP_HEADER = 1);
```

#### Load from External Stage (S3, GCS, Azure)

```sql
COPY INTO employees
FROM @my_s3_stage
FILE_FORMAT = (TYPE = 'CSV' SKIP_HEADER = 1);
```

## 3. Loading Different File Formats

### A. Loading JSON Data

1. Create Table with VARIANT Column

```sql
CREATE OR REPLACE TABLE employees_json (
    raw_data VARIANT
);
```

2. Load JSON Data

```sql
COPY INTO employees_json
FROM @my_s3_stage/employees.json
FILE_FORMAT = (TYPE = 'JSON');
```

3. Query JSON Data

```sql
SELECT raw_data:name, raw_data:salary FROM employees_json;
```

### B. Loading Parquet Data

1. Create Table for Parquet

```sql
CREATE OR REPLACE TABLE employees_parquet (
    id INT,
    name STRING,
    department STRING,
    salary FLOAT
);
```

2. Load Parquet Data

```sql
COPY INTO employees_parquet
FROM @my_s3_stage/employees.parquet
FILE_FORMAT = (TYPE = 'PARQUET');
```

3. Query Parquet Data

```sql
SELECT * FROM employees_parquet;
```

## 4. Checking Data Load Status

### Verify Data in the Table

```sql
SELECT * FROM employees;
```

### Check Copy History

```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.LOAD_HISTORY()) ORDER BY LAST_LOAD_TIME DESC;
```

## 5. Handling Errors & Best Practices

### 1. Skip Error Rows Instead of Failing

```sql
COPY INTO employees
FROM @my_internal_stage/employees.csv
FILE_FORMAT = (TYPE = 'CSV' SKIP_HEADER = 1)
ON_ERROR = CONTINUE;
```

🔹 Skips bad records and continues loading.

### 2. Load Only Specific Columns

```sql
COPY INTO employees (id, name, department)
FROM @my_s3_stage/employees.csv
FILE_FORMAT = (TYPE = 'CSV' SKIP_HEADER = 1);
```

### 3. Use Compression for Faster Load

```sql
COPY INTO employees
FROM @my_s3_stage/employees.gz
FILE_FORMAT = (TYPE = 'CSV' COMPRESSION = 'GZIP');
```

🔹 GZIP compression speeds up file transfers.

### 4. Partition Large Files for Better Performance

Instead of loading one large file, split it into multiple smaller files and load them in parallel.

## 6. Summary

✔ Staging is required before loading data into Snowflake.
✔ Use internal stages for small file loads and external stages (S3, GCS, Azure) for big data.
✔ COPY INTO is the fastest way to bulk load data.
✔ Use JSON, Parquet, or compressed CSV for better performance.
✔ Monitor load history with INFORMATION_SCHEMA.LOAD_HISTORY().
