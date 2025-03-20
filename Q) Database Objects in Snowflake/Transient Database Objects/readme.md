## Transient Database Objects in Snowflake

Transient objects in Snowflake are a middle ground between permanent and temporary objects. They persist across sessions but do not support Time Travel (beyond 0 days) or Fail-Safe, making them suitable for temporary or frequently changing data where recovery isn't critical.

### 1. Transient Databases
A transient database stores transient schemas and tables.

```sql
CREATE TRANSIENT DATABASE my_transient_db;
```
- No Fail-Safe protection.
- No Time Travel (unless explicitly set to a nonzero value).

### 2. Transient Schemas
A transient schema contains transient tables, reducing storage costs.

```sql
CREATE TRANSIENT SCHEMA my_transient_schema;
```
- Can contain both transient and permanent objects.
- Lacks Fail-Safe recovery.

### 3. Transient Tables
Transient tables persist but do not support Fail-Safe and have limited Time Travel.

```sql
CREATE TRANSIENT TABLE my_transient_table (
    id INT PRIMARY KEY,
    name STRING,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- Exists until explicitly dropped.
- Better than temporary tables for reusable but non-critical data.

### 4. Transient Stages
A transient stage stores files temporarily in Snowflake for data loading.

```sql
CREATE TRANSIENT STAGE my_transient_stage;
```
- Useful for short-term file storage.
- Reduces storage costs.

### Key Features of Transient Objects
- ✅ **Persist Across Sessions** – Unlike temporary objects.
- 🚫 **No Fail-Safe Protection** – Data is not recoverable if deleted.
- ⏳ **Limited Time Travel** – Default retention = 0 days, unless explicitly set.
- 💰 **Lower Storage Costs** – Cheaper than permanent objects.

### When to Use Transient Objects?
- **Staging Tables** for ETL/ELT pipelines.
- **Intermediate Processing** (data transformations, aggregations).
- **Frequently Updated Data** (where rollback isn't needed).
- **Temporary Storage** (staging files in transient stages).
