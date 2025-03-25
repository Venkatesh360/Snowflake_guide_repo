# Append-Only Streams in Snowflake

## Overview
Append-only streams in Snowflake track **only INSERT operations** on a table while ignoring updates and deletes. This ensures that historical data remains intact and is particularly useful in scenarios where only newly inserted records need to be processed.

## How Append-Only Streams Work
- Unlike standard streams that track **INSERT, UPDATE, and DELETE**, append-only streams only capture **newly inserted rows**.
- Updates and deletes are **not recorded**, preventing unwanted changes from affecting downstream processes.
- The stream does not reset when consumed; instead, only newly inserted rows are available in the next query.

## Creating an Append-Only Stream
```sql
CREATE OR REPLACE STREAM sales_append_stream
ON TABLE sales_raw
APPEND_ONLY = TRUE;
```

## Example Use Case: Processing New Orders
### **Step 1: Create Source Table**
```sql
CREATE TABLE sales_raw (
    id INT PRIMARY KEY,
    product STRING,
    quantity INT,
    price DECIMAL(10,2)
);
```

### **Step 2: Create Append-Only Stream**
```sql
CREATE STREAM sales_append_stream
ON TABLE sales_raw
APPEND_ONLY = TRUE;
```

### **Step 3: Insert New Records**
```sql
INSERT INTO sales_raw VALUES (1, 'Laptop', 2, 1500.00);
INSERT INTO sales_raw VALUES (2, 'Phone', 5, 700.00);
```

### **Step 4: Query Stream**
```sql
SELECT * FROM sales_append_stream;
```
**Output:**
| METADATA$ACTION | ID | Product | Quantity | Price  |
|----------------|----|---------|----------|--------|
| INSERT        | 1  | Laptop  | 2        | 1500.00 |
| INSERT        | 2  | Phone   | 5        | 700.00  |

### **Step 5: Update a Record and Query Stream Again**
```sql
UPDATE sales_raw SET quantity = 3 WHERE id = 1;
SELECT * FROM sales_append_stream;
```
**Output:** *(No results, because updates are ignored in append-only streams.)*

### **Step 6: Delete a Record and Query Stream Again**
```sql
DELETE FROM sales_raw WHERE id = 2;
SELECT * FROM sales_append_stream;
```
**Output:** *(No results, because deletes are ignored in append-only streams.)*

## Key Benefits
- **Ensures data integrity** by preventing changes from affecting previously captured records.
- **Ideal for event-driven processing** where only new data needs to be handled.
- **Optimized performance** since fewer operations are tracked compared to standard streams.

This approach is particularly useful in **ETL pipelines, auditing, and real-time data ingestion** workflows.

