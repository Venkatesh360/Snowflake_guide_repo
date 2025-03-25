### What is a Stream in Snowflake?
A stream in Snowflake is an object that tracks changes (INSERT, UPDATE, DELETE) made to a table. However, it does not store these changes itself; instead, it enables a tracking system within the original table.

When a stream is created on a table, three hidden columns are added to that table:

- **METADATA$ACTION** – Tracks the type of change (INSERT, UPDATE, DELETE).
- **METADATA$ISUPDATE** – Indicates whether the change is an UPDATE.
- **METADATA$ROW_ID** – Identifies the specific row change.

These columns are not visible in the base table but become accessible through the stream.

### How Does a Stream Work?
#### Offset Initialization:
- At the time of stream creation, an offset (a timestamp) is set to the current state of the table.
- Any changes made after this offset will be tracked.

#### Tracking Changes:
- If new rows are inserted, updated, or deleted, the stream captures these changes based on the offset.
- The offset acts as a reference point, ensuring that only new modifications are tracked.

#### Consuming the Stream:
- When we query the stream, it reveals the changes since the offset.
- When a DML operation (INSERT, UPDATE, DELETE) is performed using the stream, the offset is updated to the current state of the table.
- This means that once consumed, the stream is emptied, and future changes are tracked from this new offset.

#### Impact of Partial Consumption:
- Even if only one row is processed from the stream, the entire offset is reset, clearing the stream of all changes.
- This is crucial because streams do not allow selective row retention; they work as an all-or-nothing tracking mechanism.

### Practical Example: Creating and Using a Stream
#### Step 1: Creating a Table and Inserting Data
```sql
CREATE TABLE sales_raw_staging (
    id INT,
    product_name STRING,
    quantity INT
);

INSERT INTO sales_raw_staging VALUES (1, 'Laptop', 10), (2, 'Phone', 15);
```

#### Step 2: Creating a Stream
```sql
CREATE STREAM sales_stream ON TABLE sales_raw_staging;
```
This enables change tracking.

#### Step 3: Making Changes to the Table
```sql
INSERT INTO sales_raw_staging VALUES (3, 'Tablet', 5), (4, 'Monitor', 8);
```
These changes are now captured by `sales_stream`.

#### Step 4: Querying the Stream
```sql
SELECT * FROM sales_stream;
```
This retrieves all changes since the offset.

#### Step 5: Consuming the Stream
```sql
INSERT INTO sales_final_table SELECT * FROM sales_stream;
```
**Important:** This resets the stream because the offset is updated to the current moment.

#### Step 6: Querying the Stream Again
```sql
SELECT * FROM sales_stream;
```
The stream is empty because all changes were processed.

### Handling DELETE, UPDATE, and INSERT Together
A stream helps in implementing CDC (Change Data Capture), allowing us to handle INSERT, UPDATE, and DELETE together.

```sql
MERGE INTO sales_final_table AS target
USING sales_stream AS source
ON target.id = source.id
WHEN MATCHED AND source.METADATA$ACTION = 'DELETE' THEN DELETE
WHEN MATCHED AND source.METADATA$ACTION = 'UPDATE' THEN UPDATE SET 
    target.product_name = source.product_name,
    target.quantity = source.quantity
WHEN NOT MATCHED THEN INSERT (id, product_name, quantity)
    VALUES (source.id, source.product_name, source.quantity);
```
This ensures that all operations (INSERT, UPDATE, DELETE) are handled correctly.

### Using Streams with Tasks
To automate stream consumption, we can use Snowflake Tasks.

```sql
CREATE TASK process_sales_stream
    WAREHOUSE = my_warehouse
    SCHEDULE = '1 MINUTE'
AS
    MERGE INTO sales_final_table AS target
    USING sales_stream AS source
    ON target.id = source.id
    WHEN MATCHED AND source.METADATA$ACTION = 'DELETE' THEN DELETE
    WHEN MATCHED AND source.METADATA$ACTION = 'UPDATE' THEN UPDATE SET 
        target.product_name = source.product_name,
        target.quantity = source.quantity
    WHEN NOT MATCHED THEN INSERT (id, product_name, quantity)
        VALUES (source.id, source.product_name, source.quantity);
```
This runs every minute, ensuring that all stream changes are automatically processed.

### Key Takeaways
✔ Streams track changes but do not store data themselves.
✔ Offset determines what changes are captured—once consumed, the stream is reset.
✔ DML operations reset the offset, meaning the stream is emptied after use.
✔ Streams + Tasks automate change processing, making them useful for ETL workflows.