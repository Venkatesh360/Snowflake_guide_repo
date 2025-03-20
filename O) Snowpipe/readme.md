## Snowpipe in Snowflake

### Overview

Snowpipe is a continuous data ingestion service in Snowflake that allows you to load data from external cloud storage (AWS S3, Azure Blob, or Google Cloud Storage) into Snowflake tables **automatically and in near real-time**. It eliminates the need for manual batch loading by continuously detecting new files and ingesting them with minimal latency.

### How Snowpipe Works

1.**Stage the Data**

- Upload files to an external cloud storage service or a Snowflake internal stage. 2.**Create a Pipe**
- Define a PIPE object that specifies the source location and target table.

3. **Automatically Load Data**

- Snowpipe **monitors the storage location** and loads new data automatically when files arrive.

- It uses **event notifications** (AWS S3 EventBridge, Azure Event Grid, Google Pub/Sub) or manual triggering using the `COPY INTO` command.

### Key Features

- **Automated Loading** – No need to manually run COPY INTO commands.
- **Low Latency** – Near real-time ingestion of new data.
- **Pay-Per-Use** – Charged only for the compute resources used.
- **Scalability** – Handles large-scale streaming data seamlessly.

### Example: Setting Up Snowpipe

1. **Create a Target Table**

```sql

CREATE OR REPLACE TABLE my_table (
    id INT,
    name STRING,
    created_at TIMESTAMP
);
```

2. **Create a Stage (Cloud Storage Connection)**

```sql
CREATE OR REPLACE STAGE my_stage
URL = 's3://my-bucket/path/'
CREDENTIALS = (AWS_KEY_ID='your_key' AWS_SECRET_KEY='your_secret');

```

3. **Create a Snowpipe for Auto-Loading**

```sql
CREATE OR REPLACE PIPE my_pipe
AUTO_INGEST = TRUE
AS
COPY INTO my_table
FROM @my_stage
FILE_FORMAT = (TYPE = 'CSV' SKIP_HEADER = 1);
```

4. **Enable Event Notifications (AWS S3)**
   Configure AWS EventBridge to notify Snowflake whenever a new file is uploaded.

5. **Manually Trigger Snowpipe** (Optional)
   If AUTO_INGEST is not enabled, you can manually trigger ingestion:

```sql

ALTER PIPE my_pipe REFRESH;
```

### Monitoring & Troubleshooting

- Check pipe status:

```sql

SHOW PIPES;
```

- View recent loads:

```sql

SELECT * FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(TABLE_NAME=>'my_table', START_TIME=>DATEADD(HOUR, -1, CURRENT_TIMESTAMP)));
```

- View errors:

```sql

SELECT * FROM TABLE(INFORMATION_SCHEMA.LOAD_HISTORY) WHERE PIPE_NAME = 'MY_PIPE';
```

## When to Use Snowpipe?

✅ When you need near real-time data ingestion.
✅ When dealing with **incremental** data loads from cloud storage.
✅ When you want **automated, event-driven** ingestion instead of batch processing.

## When NOT to Use Snowpipe?

❌ If you need **low-latency streaming**, use **Snowflake Streams & Tasks** instead.
❌ If you’re processing **historical bulk loads**, use **COPY INTO** instead of Snowpipe.

```

```
