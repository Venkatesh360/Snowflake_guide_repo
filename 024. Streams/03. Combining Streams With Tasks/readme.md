# Combining Snowflake Streams with Tasks

## Overview
Snowflake Streams track data changes (INSERT, UPDATE, DELETE) in a table, while Snowflake Tasks automate SQL execution on a schedule or event basis. Combining Streams with Tasks allows for efficient **incremental data processing** without manual intervention.

## How It Works
1. **A stream captures changes** in a source table.
2. **A task checks if the stream has new data** using `SYSTEM$STREAM_HAS_DATA()`.
3. **If data exists, the task reads from the stream** and applies transformations or loads data into a target table.
4. **Once the stream is consumed, it resets**, ensuring only new changes are processed in subsequent executions.

## Example: Automating Incremental Data Processing
### **Step 1: Create Source Table & Stream**
```sql
CREATE TABLE sales_raw (
    id INT PRIMARY KEY,
    product STRING,
    quantity INT,
    price DECIMAL(10,2)
);

CREATE STREAM sales_stream ON TABLE sales_raw;
```

### **Step 2: Create Target Table**
```sql
CREATE TABLE sales_final (
    id INT PRIMARY KEY,
    product STRING,
    quantity INT,
    price DECIMAL(10,2),
    action STRING,
    change_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### **Step 3: Create Task to Process Stream Changes**
```sql
CREATE OR REPLACE TASK process_sales_changes
WAREHOUSE = my_warehouse
SCHEDULE = '1 MINUTE'
WHEN SYSTEM$STREAM_HAS_DATA('sales_stream')
AS
INSERT INTO sales_final (id, product, quantity, price, action)
SELECT id, product, quantity, price, METADATA$ACTION
FROM sales_stream;
```

### **Step 4: Activate the Task**
```sql
ALTER TASK process_sales_changes RESUME;
```

### **Step 5: Verify Automation**
- Insert new records into `sales_raw`.
- The task will run only if there is new data in the stream.
- Query `sales_final` after the task runs to confirm data processing.

## Key Benefits
- **Automates ETL workflows** by continuously processing new data.
- **Optimizes performance** by running tasks only when there are changes.
- **Ensures incremental updates** with minimal resource consumption.
- **Eliminates manual intervention**, making data pipelines more efficient.

This integration enables seamless data streaming and processing, improving real-time analytics and data warehousing efficiency.