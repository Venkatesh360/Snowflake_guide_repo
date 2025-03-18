# Snowflake Load History

Snowflake provides **load history** tracking to monitor and debug data ingestion processes. This helps in identifying load issues, tracking failures, and optimizing performance.

## 📌 Accessing Load History
Snowflake stores load history for **14 days** and provides multiple ways to access it:

1. **Querying `LOAD_HISTORY` Table Function**
2. **Using the Web UI (History Tab)**
3. **Checking `INFORMATION_SCHEMA.LOAD_HISTORY` View**

---

## 🔍 Querying Load History with SQL
Use the `COPY_HISTORY` function to retrieve detailed load history.

### ✅ Example:
```sql
SELECT *
FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(
    TABLE_NAME => 'my_table',
    START_TIME => DATEADD('DAY', -7, CURRENT_TIMESTAMP)
));
```
- Retrieves load history for `my_table` in the past 7 days.
- Helps in tracking **failed loads, duplicate loads, and performance issues**.

---

## 📊 Viewing Load History in the UI
1. Navigate to the **Snowflake Web UI**.
2. Click on the **History** tab.
3. Filter by **Load Operations** to view recent data loads.
4. Drill down to analyze failed or partially loaded files.

---

## ⚠️ Debugging Load Failures
If a data load fails, use these steps:
- Check `ERROR_MESSAGE` in `COPY_HISTORY`.
- Look at rejected data using `VALIDATION_MODE = RETURN_ERRORS`.
- Verify **file formats, column mappings, and permissions**.

### ✅ Example: Identifying Errors
```sql
SELECT STATUS, ERROR_MESSAGE, FILE_NAME
FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(
    TABLE_NAME => 'my_table',
    START_TIME => DATEADD('DAY', -7, CURRENT_TIMESTAMP)
))
WHERE STATUS = 'LOAD_FAILED';
```
- Lists **failed loads** with error details for debugging.

---

## 🔄 Automating Load History Monitoring
- **Schedule queries** to track load success rates.
- Use **alerting mechanisms** to notify on failures.
- Integrate with **third-party monitoring tools** for deeper insights.

---

## 📌 Summary
Snowflake's **Load History** tools help monitor and debug data ingestion. By using `COPY_HISTORY`, UI insights, and automated alerts, you can ensure smooth data loading operations.

💡 **Reference**: [Snowflake Documentation](https://docs.snowflake.com)
