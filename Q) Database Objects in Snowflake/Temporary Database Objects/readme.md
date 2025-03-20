## Temporary Database Objects in Snowflake

Temporary objects in Snowflake exist only within a session and are automatically dropped when the session ends. They are useful for storing intermediate or non-persistent data without affecting permanent storage.

### 1. Temporary Tables
Temporary tables exist only for the session in which they are created.

```sql
CREATE TEMP TABLE my_temp_table (
    id INT PRIMARY KEY,
    name STRING,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

or

```sql
CREATE TEMPORARY TABLE my_temp_table (
    id INT PRIMARY KEY,
    name STRING
);
```

- **Session-scoped** – Automatically dropped at session end.
- **No Fail-Safe or Time Travel** – Cannot be recovered after session ends.
- **Cannot be shared across sessions** – Unlike transient and permanent tables.
- **Faster performance** – Optimized for short-term storage.

### 2. Temporary Views
Temporary views exist only for the session and depend on underlying tables.

```sql
CREATE TEMPORARY VIEW my_temp_view AS 
SELECT id, name FROM my_temp_table;
```

- Exists only within the session.
- If the base table is dropped, the view becomes invalid.

### 3. Temporary Stages
A temporary stage allows short-term file storage within Snowflake.

```sql
CREATE TEMPORARY STAGE my_temp_stage;
```

- Exists only during the session.
- Useful for staging data before loading into permanent tables.

### 4. Temporary UDFs (User-Defined Functions)
Snowflake allows temporary SQL or JavaScript UDFs within a session.

```sql
CREATE TEMPORARY FUNCTION square(x INT) 
RETURNS INT 
LANGUAGE SQL 
AS $$
    x * x
$$;
```

- Only available during the session.
- Automatically deleted when the session ends.

### Key Features of Temporary Objects
- ✅ **Session-based** – Automatically dropped when the session ends.
- 🚫 **No Fail-Safe or Time Travel** – Cannot be recovered after session termination.
- 🔒 **Not shared across sessions** – Unlike transient and permanent objects.
- ⚡ **Faster performance** – Ideal for temporary computations or ETL processes.

### When to Use Temporary Objects?
- **Storing intermediate query results**.
- **Processing temporary data in ETL pipelines**.
- **Avoiding unnecessary storage costs**.
- **Testing queries without affecting permanent tables**.