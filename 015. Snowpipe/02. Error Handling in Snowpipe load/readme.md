# Error Handling for Snowpipe Loads in Snowflake

When using Snowpipe, errors can occur due to various reasons, such as incorrect file formats, permission issues, or syntax errors in SQL commands. Proper error handling ensures that you can identify and fix issues quickly.

## 1. Checking Snowpipe Load History

You can use the `COPY_HISTORY` and `LOAD_HISTORY` views to monitor Snowpipe loads and identify errors.

### View Recent Load History

```sql
SELECT * 
FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(
    TABLE_NAME => 'my_table', 
    START_TIME => DATEADD(HOUR, -1, CURRENT_TIMESTAMP)
));
```

This will show all loads within the last hour, including successes and failures.

### View Failed Loads

```sql
SELECT *
FROM TABLE(INFORMATION_SCHEMA.LOAD_HISTORY)
WHERE PIPE_NAME = 'MY_PIPE'
AND STATUS = 'FAILED';
```

This will return all failed load attempts for the specified pipe.

---

## 2. Checking Specific Error Messages

If a load fails, you can check the error messages using the `VALIDATION_MODE` parameter.

### Manually Validate a File Before Loading

```sql
COPY INTO my_table
FROM @my_stage
FILE_FORMAT = (TYPE = 'CSV')
VALIDATION_MODE = 'RETURN_ERRORS';
```

This runs the `COPY INTO` command in validation mode and does not load data but returns any errors encountered.

---

## 3. Handling Common Errors

| Error Type              | Cause                                         | Solution |
|-------------------------|----------------------------------------------|----------|
| **File Format Mismatch** | The file format does not match the table schema. | Ensure that the file format in the `COPY INTO` command matches the table definition. |
| **Permission Denied**   | Snowflake cannot access the external storage. | Verify IAM roles, credentials, and permissions for the storage bucket. |
| **Duplicate Data**      | The same file is loaded multiple times.       | Use metadata columns (`METADATA$FILENAME`) to track loaded files and avoid duplicates. |
| **Column Type Mismatch** | A column type does not match the expected format. | Check the schema and use file format options (e.g., `STRIP_NULL_VALUES`, `ERROR_ON_COLUMN_COUNT_MISMATCH`). |
| **Parsing Errors**      | Data contains unexpected characters.          | Use `REPLACE_INVALID_CHARACTERS = TRUE` in file format settings. |

---

## 4. Retrying Failed Loads

If a file fails to load, Snowpipe does not automatically retry the failed ingestion. You can manually reload data by refreshing the pipe.

```sql
ALTER PIPE my_pipe REFRESH;
```

This command instructs Snowflake to reprocess all files that have not been successfully loaded.

---

## 5. Enabling Notifications for Errors

You can set up event notifications for failed Snowpipe loads using cloud services:

- **AWS S3**: Use SNS or EventBridge to trigger alerts.
- **Azure Blob**: Use Event Grid for failure notifications.
- **Google Cloud Storage**: Use Pub/Sub to detect failures.

### Example of setting up an AWS SNS Notification for failed Snowpipe loads:

```sql
CREATE NOTIFICATION INTEGRATION my_sns_integration
ENABLED = TRUE
TYPE = QUEUE
NOTIFICATION_PROVIDER = AWS_SNS
AWS_SNS_TOPIC_ARN = 'arn:aws:sns:us-east-1:123456789012:MySnowpipeFailures';
```

---

## 6. Using Query-Based Debugging

### Check the Last 5 Errors in Snowpipe Logs

```sql
SELECT *
FROM TABLE(INFORMATION_SCHEMA.LOAD_HISTORY)
WHERE PIPE_NAME = 'MY_PIPE'
ORDER BY LAST_LOAD_TIME DESC
LIMIT 5;
```

### Check Which Files Were Processed Successfully

```sql
SELECT METADATA$FILENAME, *
FROM my_table
ORDER BY created_at DESC;
```

This query helps verify which files were successfully loaded into the table.

---

## Summary of Best Practices for Error Handling in Snowpipe

✅ Use `COPY_HISTORY` and `LOAD_HISTORY` views to track errors.  
✅ Validate data before ingestion using `VALIDATION_MODE = 'RETURN_ERRORS'`.  
✅ Ensure correct file formats and table schemas match.  
✅ Use `ALTER PIPE REFRESH;` to manually retry failed loads.  
✅ Set up event notifications for automatic error alerts.  
✅ Log metadata columns like `METADATA$FILENAME` to track file ingestion.

This should help you efficiently monitor, debug, and recover from Snowpipe errors. Let me know if you need any more details!
