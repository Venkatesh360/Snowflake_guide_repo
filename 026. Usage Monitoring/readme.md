# Monitoring Account Usage in Snowflake with Queries

Here are detailed SQL queries to monitor various aspects of your Snowflake account:

## 1. Monitor Query History
To analyze query execution time, users, and resource consumption:

```sql
SELECT query_id, user_name, warehouse_name, execution_status, start_time, end_time, total_elapsed_time 
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
WHERE start_time >= DATEADD(DAY, -7, CURRENT_TIMESTAMP)
ORDER BY start_time DESC
LIMIT 50;
```
🔹 This fetches the last 7 days of query history.

## 2. Monitor Login History
To check user logins and failed attempts:

```sql
SELECT event_timestamp, user_name, client_ip, event_type, error_message 
FROM SNOWFLAKE.ACCOUNT_USAGE.LOGIN_HISTORY
WHERE event_timestamp >= DATEADD(DAY, -7, CURRENT_TIMESTAMP)
ORDER BY event_timestamp DESC;
```
🔹 Useful for detecting unauthorized login attempts.

## 3. Monitor Warehouse Credit Usage
To track how much Snowflake compute (credits) a warehouse is using:

```sql
SELECT warehouse_name, sum(credits_used) AS total_credits, date_trunc('day', start_time) AS usage_date
FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY
WHERE start_time >= DATEADD(DAY, -30, CURRENT_TIMESTAMP)
GROUP BY warehouse_name, usage_date
ORDER BY usage_date DESC;
```
🔹 Helps identify high-cost warehouses.

## 4. Monitor Database Storage Usage
To check how much storage a database is consuming:

```sql
SELECT database_name, avg(used_bytes)/1024/1024/1024 AS avg_storage_gb, date_trunc('day', usage_date) AS usage_day
FROM SNOWFLAKE.ACCOUNT_USAGE.DATABASE_STORAGE_USAGE_HISTORY
WHERE usage_date >= DATEADD(DAY, -30, CURRENT_DATE)
GROUP BY database_name, usage_day
ORDER BY usage_day DESC;
```
🔹 Displays storage usage in GB per day.

## 5. Monitor User Activity (Sessions)
To see active and past user sessions:

```sql
SELECT user_name, session_id, warehouse_name, start_time, end_time
FROM SNOWFLAKE.ACCOUNT_USAGE.SESSIONS
WHERE start_time >= DATEADD(DAY, -7, CURRENT_TIMESTAMP)
ORDER BY start_time DESC;
```
🔹 Helps track active users and session durations.

## 6. Monitor Costs Usage (Compute & Storage)
To track overall account costs based on storage and compute usage:

```sql
SELECT service_type, sum(credits_used) AS total_credits, date_trunc('day', usage_date) AS usage_day
FROM SNOWFLAKE.ACCOUNT_USAGE.COST_USAGE_HISTORY
WHERE usage_date >= DATEADD(DAY, -30, CURRENT_DATE)
GROUP BY service_type, usage_day
ORDER BY usage_day DESC;
```
🔹 Helps estimate monthly Snowflake costs.

## 7. Monitor Failed Queries (Errors & Timeouts)
To find queries that failed due to errors or timeouts:

```sql
SELECT query_id, user_name, error_code, error_message, start_time
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
WHERE error_code IS NOT NULL
AND start_time >= DATEADD(DAY, -7, CURRENT_TIMESTAMP)
ORDER BY start_time DESC;
```
🔹 Helps debug errors in queries.

## 8. Set Up a Resource Monitor
To automatically suspend a warehouse if it exceeds a certain credit limit:

```sql
CREATE RESOURCE MONITOR my_monitor
WITH CREDIT_QUOTA = 1000  -- Max credits allowed
TRIGGERS ON 80 PERCENT DO SUSPEND
ON 100 PERCENT DO SUSPEND_IMMEDIATE;
```
🔹 This will suspend the warehouse when 80% of credits are used and force-suspend at 100%.

## 9. Monitor User Permissions & Roles
To check user access levels:

```sql
SELECT grantee_name, granted_on, name, privilege, granted_by 
FROM SNOWFLAKE.ACCOUNT_USAGE.GRANTS_TO_USERS
ORDER BY granted_on DESC;
```
🔹 Ensures correct permissions are assigned.

## Table Storage

This query retrieves storage metrics for all tables in the Snowflake account, providing insights into how much space each table consumes.

```sql
SELECT * FROM "SNOWFLAKE"."ACCOUNT_USAGE"."TABLE_STORAGE_METRICS";
```

## How much is queried in databases

The first query retrieves historical query execution data, useful for analyzing database activity and user queries.

```sql
SELECT * FROM "SNOWFLAKE"."ACCOUNT_USAGE"."QUERY_HISTORY";
```

The second query aggregates the number of queries and the total cloud service credits consumed per database, helping track database-specific query load and associated costs.

```sql
SELECT 
    DATABASE_NAME,
    COUNT(*) AS NUMBER_OF_QUERIES,
    SUM(CREDITS_USED_CLOUD_SERVICES)
FROM "SNOWFLAKE"."ACCOUNT_USAGE"."QUERY_HISTORY"
GROUP BY DATABASE_NAME;
```

## Usage of credits by warehouses

This query retrieves metering history for Snowflake warehouses, showing how many credits each warehouse has consumed.

```sql
SELECT * FROM "SNOWFLAKE"."ACCOUNT_USAGE"."WAREHOUSE_METERING_HISTORY";
```

### Usage of credits by warehouses (Grouped by day)

This query groups warehouse credit usage by day, helping track daily resource consumption trends.

```sql
SELECT 
    DATE(START_TIME),
    SUM(CREDITS_USED)
FROM "SNOWFLAKE"."ACCOUNT_USAGE"."WAREHOUSE_METERING_HISTORY"
GROUP BY DATE(START_TIME);
```

### Usage of credits by warehouses (Grouped by warehouse)

This query groups warehouse credit usage by warehouse name, allowing comparison of resource consumption across different warehouses.

```sql
SELECT
    WAREHOUSE_NAME,
    SUM(CREDITS_USED)
FROM "SNOWFLAKE"."ACCOUNT_USAGE"."WAREHOUSE_METERING_HISTORY"
GROUP BY WAREHOUSE_NAME;
```

### Usage of credits by warehouses (Grouped by warehouse & day)

This query provides a detailed breakdown of credit usage by warehouse and day, useful for tracking daily usage per warehouse.

```sql
SELECT
    DATE(START_TIME),
    WAREHOUSE_NAME,
    SUM(CREDITS_USED)
FROM "SNOWFLAKE"."ACCOUNT_USAGE"."WAREHOUSE_METERING_HISTORY"
GROUP BY WAREHOUSE_NAME, DATE(START_TIME);