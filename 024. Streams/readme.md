# Streams in Snowflake

## 1. What is a Stream in Snowflake?
A stream in Snowflake is a change tracking object that captures INSERTS, UPDATES, and DELETES on a table. It helps implement Change Data Capture (CDC) without modifying the original table.

## 2. Types of Streams in Snowflake
| Stream Type          | Description                                     |
|----------------------|-------------------------------------------------|
| Standard Stream     | Tracks changes at the row level.               |
| Append-Only Stream  | Tracks only INSERTS (ignores updates/deletes).  |
| Insert-Only Stream  | Similar to append-only but for table clones.    |

## 3. Creating and Using a Stream

### Step 1: Create a Table
```sql
CREATE OR REPLACE TABLE employees (
    id INT,
    name STRING,
    salary NUMBER
);
```

### Step 2: Create a Stream on the Table
```sql
CREATE OR REPLACE STREAM employees_stream
ON TABLE employees;
```
✔ This stream tracks changes in the employees table.

## 4. Capturing and Viewing Changes

### Step 3: Perform DML Operations
```sql
INSERT INTO employees VALUES (1, 'Alice', 50000);
UPDATE employees SET salary = 60000 WHERE id = 1;
DELETE FROM employees WHERE id = 1;
```

### Step 4: Check the Stream for Changes
```sql
SELECT * FROM employees_stream;
```
| ID | NAME  | SALARY | METADATA$ACTION | METADATA$ISUPDATE |
|----|-------|--------|-----------------|-------------------|
| 1  | Alice | 50000  | INSERT          | FALSE            |
| 1  | Alice | 60000  | DELETE          | TRUE             |
| 1  | Alice | 60000  | INSERT          | TRUE             |
| 1  | Alice | NULL   | DELETE          | FALSE            |

✔ The `METADATA$ACTION` column shows INSERT and DELETE changes.
✔ Updates appear as DELETE + INSERT (because streams track row-level changes).

## 5. Applying Stream Changes to Another Table

### Step 5: Create a History Table
```sql
CREATE OR REPLACE TABLE employees_history AS
SELECT * FROM employees WHERE 1=0;
```

### Step 6: Insert Stream Data into History Table
```sql
INSERT INTO employees_history
SELECT * FROM employees_stream;
```
✔ This step moves captured changes to another table for further analysis.

## 6. Stream Cleanup
After reading data from a stream, it does not retain history unless a task is continuously processing it.

To persist history, regularly copy stream data into another table.

## 7. Summary
✔ Streams track changes (INSERT, UPDATE, DELETE) in a table.
✔ Used for Change Data Capture (CDC) in Snowflake.
✔ Standard streams track all DML operations, while append-only streams track only inserts.
✔ Changes disappear after reading, so save them in another table if needed.
