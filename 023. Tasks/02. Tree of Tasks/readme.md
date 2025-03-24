# Tree of Tasks in Snowflake (Task Hierarchy)

## 1. What is a Task Tree?
A **Task Tree** is a hierarchical structure of tasks in Snowflake where:
- A **parent task** triggers one or more **child tasks**.
- Child tasks can have their own children, forming a hierarchical **tree structure**.
- The execution order is determined by dependencies using the **AFTER** clause.

### Example: Task Tree Structure
```
Task A (Root Task)
│
├── Task B (Child of Task A)
│   ├── Task D (Child of Task B)
│   └── Task E (Child of Task B)
│
└── Task C (Child of Task A)
    └── Task F (Child of Task C)
```
- **Task A** starts the workflow.
- **Task B** and **Task C** run after Task A.
- **Task D** and **Task E** run after Task B.
- **Task F** runs after Task C.

## 2. Creating a Task Tree in Snowflake

### Step 1: Create the Root Task
```sql
CREATE TASK task_A
WAREHOUSE = my_warehouse
SCHEDULE = 'USING CRON 0 6 * * * UTC'
AS
INSERT INTO logs VALUES ('Task A executed', CURRENT_TIMESTAMP);
```
🔹 This is the root task that runs at **6 AM UTC** daily.

### Step 2: Create Child Tasks
#### Task B (Runs After Task A)
```sql
CREATE TASK task_B
WAREHOUSE = my_warehouse
AFTER task_A
AS
INSERT INTO logs VALUES ('Task B executed', CURRENT_TIMESTAMP);
```
🔹 Task B runs automatically after **Task A** finishes.

#### Task C (Runs After Task A)
```sql
CREATE TASK task_C
WAREHOUSE = my_warehouse
AFTER task_A
AS
INSERT INTO logs VALUES ('Task C executed', CURRENT_TIMESTAMP);
```
🔹 Task C runs in **parallel** with Task B after **Task A**.

### Step 3: Create Grandchild Tasks
#### Task D (Runs After Task B)
```sql
CREATE TASK task_D
WAREHOUSE = my_warehouse
AFTER task_B
AS
INSERT INTO logs VALUES ('Task D executed', CURRENT_TIMESTAMP);
```
#### Task E (Runs After Task B)
```sql
CREATE TASK task_E
WAREHOUSE = my_warehouse
AFTER task_B
AS
INSERT INTO logs VALUES ('Task E executed', CURRENT_TIMESTAMP);
```
#### Task F (Runs After Task C)
```sql
CREATE TASK task_F
WAREHOUSE = my_warehouse
AFTER task_C
AS
INSERT INTO logs VALUES ('Task F executed', CURRENT_TIMESTAMP);
```

## 3. Activating the Task Tree
By default, tasks are created in a **suspended state**. You need to activate them.

### Resume the Root Task
```sql
ALTER TASK task_A RESUME;
```
🔹 This automatically triggers the entire task tree when **Task A** executes.

## 4. Checking Task Execution & Hierarchy
### Show All Tasks
```sql
SHOW TASKS;
```
💡 Use this to verify task **status** and **hierarchy**.

### Check Task Execution History
```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY());
```
💡 Use this to check **execution timestamps**, **failures**, and **task order**.

## 5. Best Practices for Task Trees
✅ **Keep task dependencies simple** → Avoid unnecessary complexity.
✅ **Use small warehouses for lightweight tasks** → Saves cost.
✅ **Ensure parent tasks complete before child tasks run** → Prevents data inconsistency.
✅ **Monitor execution logs (TASK_HISTORY)** → Debug failures.
✅ **Suspend unnecessary tasks to reduce costs.**

## 6. Recursive Way to Resume or Suspend All Tasks
Manually resuming or suspending each task can be tedious. You can automate this process.

### Recursive Resume of All Tasks
```sql
DECLARE done BOOLEAN DEFAULT FALSE;
BEGIN
    FOR task_rec IN 
        (SELECT name FROM snowflake.account_usage.tasks WHERE state = 'SUSPENDED') 
    DO
        LET sql_cmd STRING = 'ALTER TASK ' || task_rec.name || ' RESUME';
        EXECUTE IMMEDIATE sql_cmd;
    END FOR;
END;
```

### Recursive Suspend of All Tasks
```sql
DECLARE done BOOLEAN DEFAULT FALSE;
BEGIN
    FOR task_rec IN 
        (SELECT name FROM snowflake.account_usage.tasks WHERE state = 'STARTED') 
    DO
        LET sql_cmd STRING = 'ALTER TASK ' || task_rec.name || ' SUSPEND';
        EXECUTE IMMEDIATE sql_cmd;
    END FOR;
END;
```

## 7. Alternative SQL Approach
If you prefer a simpler approach, generate SQL statements dynamically:

### Generate SQL to Resume All Tasks
```sql
SELECT 'ALTER TASK ' || name || ' RESUME;' 
FROM snowflake.account_usage.tasks 
WHERE state = 'SUSPENDED';
```

### Generate SQL to Suspend All Tasks
```sql
SELECT 'ALTER TASK ' || name || ' SUSPEND;' 
FROM snowflake.account_usage.tasks 
WHERE state = 'STARTED';
```

## 8. Stored Procedures for Task Management
Instead of manually running queries, use **stored procedures** for automation.

### Stored Procedure to Resume All Tasks
```sql
CREATE OR REPLACE PROCEDURE resume_all_tasks()
RETURNS STRING
LANGUAGE SQL
EXECUTE AS CALLER
AS 
$$
DECLARE task_name STRING;
BEGIN
    FOR task_rec IN 
        (SELECT name FROM snowflake.account_usage.tasks WHERE state = 'SUSPENDED') 
    DO
        LET sql_cmd STRING = 'ALTER TASK ' || task_rec.name || ' RESUME';
        EXECUTE IMMEDIATE sql_cmd;
    END FOR;
    
    RETURN 'All suspended tasks have been resumed.';
END;
$$;
```
#### Run the Procedure
```sql
CALL resume_all_tasks();
```

### Stored Procedure to Suspend All Tasks
```sql
CREATE OR REPLACE PROCEDURE suspend_all_tasks()
RETURNS STRING
LANGUAGE SQL
EXECUTE AS CALLER
AS 
$$
DECLARE task_name STRING;
BEGIN
    FOR task_rec IN 
        (SELECT name FROM snowflake.account_usage.tasks WHERE state = 'STARTED') 
    DO
        LET sql_cmd STRING = 'ALTER TASK ' || task_rec.name || ' SUSPEND';
        EXECUTE IMMEDIATE sql_cmd;
    END FOR;
    
    RETURN 'All running tasks have been suspended.';
END;
$$;
```
#### Run the Procedure
```sql
CALL suspend_all_tasks();
```

## 🚀 Summary
✔ **Task Trees** allow hierarchical scheduling of tasks.
✔ **Parent-child dependencies** are defined using `AFTER`.
✔ **Activating the root task** triggers the entire workflow.
✔ **Monitor execution** with `SHOW TASKS` and `TASK_HISTORY`.
✔ **Automate task suspension/resumption** using stored procedures.