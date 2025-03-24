Understanding Stream Offsets in Snowflake

1️⃣ What is a Stream Offset?
A stream offset in Snowflake is an internal marker that keeps track of the last processed data in a stream. It ensures that each query on the stream fetches only new changes since the last read.

Key behaviors of stream offsets:
✔ Stream offset updates automatically after a query reads the stream.
✔ Once read, old records are no longer available in the stream (except for append-only streams).
✔ Streams are transactionally consistent, meaning they track changes exactly as they happened.

2️⃣ How Stream Offset Works in Practice
1️⃣ Create a table and a stream:

```sql
CREATE OR REPLACE TABLE employees (
    id INT,
    name STRING,
    salary NUMBER
);

CREATE OR REPLACE STREAM employees_stream ON TABLE employees;
```

2️⃣ Insert some data into the table:

```sql
INSERT INTO employees VALUES (1, 'Alice', 50000);
INSERT INTO employees VALUES (2, 'Bob', 60000);
```

3️⃣ Check the stream content (First Read):

```sql
SELECT * FROM employees_stream;
```
✔ Result:

| ID | NAME  | SALARY | METADATA$ACTION |
|----|------|--------|----------------|
| 1  | Alice | 50000  | INSERT         |
| 2  | Bob   | 60000  | INSERT         |

4️⃣ Query the stream again (Second Read):

```sql
SELECT * FROM employees_stream;
```
✔ Result:
✅ No records appear!
✔ Why? The stream offset has moved forward after the first read, so no old records are available.

3️⃣ Stream Offset and Append-Only Streams
✔ In a standard stream, the offset updates after each read, making older records unavailable.
✔ Append-only streams behave differently—they don’t remove old records after reading.

Creating an Append-Only Stream:

```sql
CREATE OR REPLACE STREAM employees_stream_append
ON TABLE employees
APPEND_ONLY = TRUE;
```
✔ This only tracks inserts and doesn’t delete old records from the stream after reading.

4️⃣ Avoid Losing Stream Data Using a Task
If you need to retain stream data beyond a single query, store it in another table before the offset updates.

✅ Create a History Table:

```sql
CREATE OR REPLACE TABLE employees_history AS 
SELECT * FROM employees WHERE 1=0;
```

✅ Insert Stream Data into History Table Before Offset Moves:

```sql
INSERT INTO employees_history
SELECT * FROM employees_stream;
```
✔ This ensures no data is lost, even after the stream offset updates.

5️⃣ Summary of Stream Offsets
✔ Streams track changes until read—after reading, data disappears (except in append-only streams).
✔ Stream offset ensures only new changes appear in subsequent queries.
✔ To persist stream data, move it to another table before the offset updates.
✔ Append-only streams don’t remove records, making them useful for auditing inserts.
