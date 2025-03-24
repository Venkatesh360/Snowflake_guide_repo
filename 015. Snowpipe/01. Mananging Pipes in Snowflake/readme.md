# Managing Pipes in Snowflake

Snowflake pipes are objects that define continuous data ingestion using Snowpipe. Managing pipes efficiently ensures smooth, automated data loading while allowing control over monitoring, pausing, resuming, and deleting pipes when necessary.

## 1. Listing and Viewing Pipes

To see all pipes in your Snowflake account, use:

```sql
SHOW PIPES;
```

To describe a specific pipe and see its details:

```sql
DESC PIPE my_pipe;
```

---

## 2. Pausing and Resuming Pipes

### Pause a Pipe
Pausing a pipe stops it from automatically ingesting new data but does not delete it.

```sql
ALTER PIPE my_pipe SET PIPE_EXECUTION_PAUSED = TRUE;
```

### Resume a Pipe
To resume ingestion after pausing:

```sql
ALTER PIPE my_pipe SET PIPE_EXECUTION_PAUSED = FALSE;
```

---

## 3. Refreshing a Pipe Manually

If `AUTO_INGEST = FALSE`, or if you need to manually trigger Snowpipe, use:

```sql
ALTER PIPE my_pipe REFRESH;
```

This command forces Snowflake to check for new files in the stage and load them if they haven't been processed.

---

## 4. Dropping (Deleting) a Pipe

If a pipe is no longer needed, you can permanently delete it:

```sql
DROP PIPE my_pipe;
```

🚨 **Warning**: This removes the pipe, and Snowflake will no longer monitor the stage for new files.

### What Happens to Metadata When a Pipe is Dropped?
✅ Metadata is **NOT lost** when a pipe is dropped – Snowflake retains ingestion history in the `COPY_HISTORY` and `LOAD_HISTORY` views.  
✅ If a new pipe is created with the same stage and target table, Snowflake does **not** reload already processed files (unless the file names change).  
✅ If you want to reload files after recreating a pipe, rename the files or modify the table schema to force reprocessing.  

---

## 5. Checking Pipe Load History

To monitor recent Snowpipe activity, use:

```sql
SELECT * 
FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(
    TABLE_NAME => 'my_table', 
    START_TIME => DATEADD(HOUR, -1, CURRENT_TIMESTAMP)
));
```

To check the last failed load attempts:

```sql
SELECT *
FROM TABLE(INFORMATION_SCHEMA.LOAD_HISTORY)
WHERE PIPE_NAME = 'MY_PIPE'
AND STATUS = 'FAILED';
```

---

## 6. Managing Event Notifications (AWS, Azure, GCP)

If your Snowpipe uses event notifications (e.g., AWS S3 EventBridge, Azure Event Grid, or Google Pub/Sub), ensure that:

✅ Permissions are correct for Snowflake to access the cloud storage.  
✅ The integration is active (use `SHOW INTEGRATIONS;`).  
✅ The notification service is correctly configured to trigger ingestion.  

### To disable event ingestion (while keeping the pipe):

```sql
ALTER PIPE my_pipe SET AUTO_INGEST = FALSE;
```

### To re-enable event ingestion:

```sql
ALTER PIPE my_pipe SET AUTO_INGEST = TRUE;
```

---

## 7. Best Practices for Managing Pipes

✔ Use `SHOW PIPES` regularly to check the status of active pipes.  
✔ Pause pipes instead of dropping them if you may need them later.  
✔ Manually refresh pipes (`ALTER PIPE REFRESH`) when debugging ingestion issues.  
✔ Monitor `COPY_HISTORY` and `LOAD_HISTORY` to track file ingestion and troubleshoot errors.  
✔ Set up event notifications correctly for auto-ingestion if using AWS, Azure, or GCP storage.  
✔ Metadata is not lost when dropping a pipe, but to force reprocessing, rename files or alter the target table.  

By effectively managing Snowflake pipes, you can ensure efficient, automated, and error-free data ingestion. Let me know if you need more details!
