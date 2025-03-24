# Tasks in Snowflake

## 1️⃣ What is a Task in Snowflake?
A task in Snowflake is a scheduled, automated execution of SQL statements. Tasks are commonly used for ETL (Extract, Transform, Load) operations, data refresh, and scheduled maintenance.

## 2️⃣ How to Create a Task?

### ✅ Basic Task Example (Runs Every 10 Minutes)
```sql
CREATE TASK refresh_sales_data
WAREHOUSE = my_warehouse
SCHEDULE = '10 MINUTE'
AS
INSERT INTO sales_agg
SELECT region, SUM(amount) 
FROM sales 
GROUP BY region;
```
🔹 Runs every 10 minutes and inserts aggregated sales data into `sales_agg`.
🔹 Uses `my_warehouse` to execute the task.

## 3️⃣ How to Activate a Task?
After creating a task, you must resume it to start execution:

```sql
ALTER TASK refresh_sales_data RESUME;
```
To pause the task:

```sql
ALTER TASK refresh_sales_data SUSPEND;
```

## 4️⃣ Scheduling a Task
Snowflake tasks support different scheduling options:

| Schedule Type   | Example                                  | Description                            |
|----------------|------------------------------------------|----------------------------------------|
| **Fixed Interval** | `SCHEDULE = '10 MINUTE'`               | Runs every 10 minutes.                |
| **Cron Expression** | `SCHEDULE = 'USING CRON 0 6 * * * UTC'` | Runs daily at 6 AM UTC.               |
| **Event-Triggered** | Child Task (Chained Task)             | Runs only after another task finishes. |

## 5️⃣ Task Chains (Parent-Child Tasks)
You can chain multiple tasks to execute in sequence.

### ✅ Example: Task Chain (Step-by-Step Execution)

#### Step 1: Create Parent Task
```sql
CREATE TASK task_1 
WAREHOUSE = my_warehouse
SCHEDULE = 'USING CRON 0 6 * * * UTC'
AS
INSERT INTO daily_sales SELECT * FROM sales WHERE sale_date = CURRENT_DATE;
```

#### Step 2: Create a Dependent Child Task
```sql
CREATE TASK task_2 
WAREHOUSE = my_warehouse
AFTER task_1
AS
INSERT INTO reports SELECT * FROM daily_sales WHERE amount > 500;
```
🔹 `task_2` runs only after `task_1` completes successfully.
🔹 Ensures data is available before running dependent processes.

## 6️⃣ Checking Task Status
You can check task status using:

```sql
SHOW TASKS;
```
For details on a specific task:

```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY())
WHERE NAME = 'refresh_sales_data';
```

## 7️⃣ Cost of Tasks
Tasks consume compute resources based on the warehouse used.

| Cost Component  | Description |
|---------------|-------------|
| **Compute Cost** | Tasks run using a virtual warehouse, incurring compute costs. |
| **Idle Time Cost** | If a warehouse is not auto-suspended, it may remain running. |
| **Storage Cost** | If tasks generate logs or temporary data, storage costs may apply. |

💡 **Best Practice:** Use a small warehouse for lightweight tasks and enable auto-suspend to save costs.

## 8️⃣ Limitations of Snowflake Tasks

| Limitation | Description |
|------------|-------------|
| **No Real-Time Execution** | Minimum schedule interval is 1 minute. |
| **Single Statement Execution** | A task can execute only one SQL statement (use stored procedures for multiple). |
| **Cannot Run Python Directly** | Only SQL statements are supported (use Snowpark for Python). |
| **Task Failure Handling** | If a task fails, Snowflake does not automatically retry it. |

## 🚀 Summary
✔ Tasks automate SQL execution on a schedule.  
✔ They run on virtual warehouses and incur compute costs.  
✔ Tasks can be chained for complex workflows.  
✔ Use `ALTER TASK RESUME/SUSPEND` to control execution.  
✔ Best suited for ETL, data refresh, and scheduled maintenance.
