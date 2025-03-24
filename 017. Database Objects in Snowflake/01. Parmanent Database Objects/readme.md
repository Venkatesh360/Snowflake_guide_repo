## Permanent Database Objects in Snowflake

In Snowflake, permanent database objects are those that persist beyond a session and remain stored until explicitly dropped. These objects are fully recoverable using Snowflake's Time Travel and Fail-Safe features.

### Types of Permanent Database Objects in Snowflake

#### 1. Databases
A permanent database stores schemas, tables, views, and other database objects.

```sql
CREATE DATABASE my_database;
```
- Remains until explicitly dropped.
- Supports Time Travel (default retention: 1 day, extendable to 90 days for Enterprise+).

#### 2. Schemas
A schema organizes database objects like tables and views.

```sql
CREATE SCHEMA my_schema;
```
- Exists permanently unless explicitly dropped.
- Contains permanent, transient, and temporary objects.

#### 3. Permanent Tables
Unlike temporary or transient tables, permanent tables support Time Travel and Fail-Safe.

```sql
CREATE TABLE my_table (
    id INT PRIMARY KEY,
    name STRING,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- Data remains even after a session ends.
- Supports Time Travel (default: 1 day).

#### 4. Permanent Views
Views store SQL queries that can be referenced later.

```sql
CREATE VIEW my_view AS
SELECT id, name FROM my_table WHERE created_at > CURRENT_DATE - INTERVAL '7 days';
```
- Views do not store data but refer to the base table.
- If the base table is dropped, the view becomes invalid.

#### 5. Stored Procedures
Stored procedures encapsulate logic and allow procedural execution in Snowflake.

```sql
CREATE OR REPLACE PROCEDURE my_proc()
RETURNS STRING
LANGUAGE SQL
AS
$$
    RETURN 'Hello, Snowflake!';
$$;
```
- Remains in the database until explicitly dropped.

#### 6. User-Defined Functions (UDFs)
UDFs allow custom processing logic within queries.

```sql
CREATE OR REPLACE FUNCTION square(x INT)
RETURNS INT
LANGUAGE SQL
AS
$$
    x * x
$$;
```
- Can be scalar, table, or JavaScript-based.

#### 7. Sequences
Sequences generate unique numeric values.

```sql
CREATE SEQUENCE my_sequence START WITH 1 INCREMENT BY 1;
```
- Used to generate unique IDs.

#### 8. Stages (for File Storage)
A permanent stage allows storing and loading files.

```sql
CREATE STAGE my_stage;
```
- Supports external storage like AWS S3 and Azure Blob.

#### 9. Streams & Tasks
- **Streams** track changes in tables for Change Data Capture (CDC).
- **Tasks** automate scheduled executions.

### Key Features of Permanent Objects
- **Persist Across Sessions** – Unlike temporary objects.
- **Support Time Travel** – Restorable up to 90 days (Enterprise Edition).
- **Subject to Fail-Safe** – Data can be recovered after retention expires.
