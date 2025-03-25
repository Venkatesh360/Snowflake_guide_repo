# Understanding Snowflake Streams and Handling Multiple Changes to the Same Record

## Overview
Snowflake Streams enable **change tracking** on a table without storing the actual data. Instead, they store an **offset** that captures changes (INSERT, UPDATE, DELETE) since the stream was last queried. Streams are useful for **incremental data processing** and automating tasks with Snowflake Tasks.

## Behavior When Multiple Changes Occur to the Same Record
Snowflake Streams do **not** retain multiple historical changes for the same record. Instead, they only keep **the latest change** before the stream is queried or consumed.

### **How Changes Are Captured**

| Action Sequence | Stored in Stream? |
|-----------------|------------------|
| `INSERT → UPDATE → DELETE` (before query) | Only `DELETE` is retained |
| `INSERT → UPDATE` (before query) | Only final `UPDATE` is stored |
| `INSERT → Query Stream → UPDATE` | `INSERT` appears first, then `UPDATE` after next query |

### **Example Demonstration**
#### **1. Create Table and Stream**
```sql
CREATE TABLE sales (
    id INT PRIMARY KEY,
    product STRING,
    quantity INT
);

CREATE STREAM sales_stream ON TABLE sales;
```

#### **2. Insert a Record**
```sql
INSERT INTO sales VALUES (1, 'Laptop', 10);
```
Querying the stream:
```sql
SELECT * FROM sales_stream;
```
**Output:**
| METADATA$ACTION | ID | Product | Quantity |
|----------------|----|---------|----------|
| INSERT        | 1  | Laptop  | 10       |

#### **3. Update the Record Twice**
```sql
UPDATE sales SET quantity = 12 WHERE id = 1;
UPDATE sales SET quantity = 15 WHERE id = 1;
```
Querying the stream now:
```sql
SELECT * FROM sales_stream;
```
**Output:**
| METADATA$ACTION | ID | Product | Quantity |
|----------------|----|---------|----------|
| UPDATE        | 1  | Laptop  | 15       |

❌ **The first update (`quantity = 12`) is lost because Snowflake only keeps the latest version before querying the stream.**

#### **4. Delete the Record**
```sql
DELETE FROM sales WHERE id = 1;
```
Querying the stream:
```sql
SELECT * FROM sales_stream;
```
**Output:**
| METADATA$ACTION | ID | Product | Quantity |
|----------------|----|---------|----------|
| DELETE        | 1  | NULL    | NULL     |

❌ **Previous INSERT and UPDATE actions are gone because DELETE is now the final state before consumption.**

## **Key Takeaways**
- **Streams only store the latest change per row before being queried.**
- **Intermediate updates are not preserved**—only the last action before querying is tracked.
- **Deletes override previous changes** to the same row in the stream.
- **Once the stream is queried, the offset updates and resets the stream**, removing previous changes.

## **Tracking Complete History (Workaround)**
If historical tracking of **all** changes is required, consider:
1. **History Tables:** Manually logging changes in an audit table.
2. **Snowflake Time Travel:** Query previous states of the table with `AT` or `BEFORE` clauses.
3. **Using CDC (Change Data Capture):** Implement external tools like Debezium for full change tracking.

This documentation provides a structured understanding of Snowflake Streams and how they handle multiple changes efficiently.

