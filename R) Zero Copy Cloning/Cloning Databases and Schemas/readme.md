# Cloning Databases and Schemas in Snowflake

## 1. Understanding Schema and Database Cloning
After exploring table cloning, we can also clone entire schemas and databases in Snowflake. Cloning at these levels follows the same zero-copy cloning principle, making the process fast and storage-efficient.

### Key Concepts:
- **Schema Cloning**: Duplicates all objects (tables, views, stages, etc.) within a schema.
- **Database Cloning**: Clones all schemas and objects within a database.
- **Zero-Copy Mechanism**: Initial cloning does not consume additional storage. New storage is used only when modifications are made.

## 2. Cloning a Schema
### Step 1: Creating a Transient Schema
To avoid unnecessary storage consumption, we use a transient schema (which does not have fail-safe storage).

```sql
CREATE TRANSIENT SCHEMA copied_schema CLONE our_first_db.public;
```
- This creates `copied_schema` within the same database (`our_first_db`).
- The original schema is `public`.
- Transient schemas are ideal for temporary work, as they do not retain fail-safe copies.

### Step 2: Verifying the Cloned Schema
After execution:
- The schema `copied_schema` appears under `our_first_db`.
- It includes all tables and objects from `public`.
- Querying the copied `customers` table confirms data has been copied:

```sql
SELECT * FROM copied_schema.customers;
```

## 3. Cloning Across Different Databases
Cloning is not restricted to the same database; you can clone a schema into another database.

```sql
CREATE TRANSIENT SCHEMA external_stages_copied CLONE manage_db.external_stages;
```
- This clones `external_stages` from `manage_db` into `our_first_db`.
- The new schema is named `external_stages_copied`.
- All objects (such as external stages) are also copied.

### Step 1: Checking if Stages are Cloned
After refreshing, we can check if external stages are cloned:

```sql
SHOW STAGES IN SCHEMA our_first_db.external_stages_copied;
```
- If the stages appear, cloning was successful.

## 4. Cloning an Entire Database
Cloning a full database is useful for:
- Creating backups.
- Testing changes in an isolated environment.

### Step 1: Cloning the Database
```sql
CREATE TRANSIENT DATABASE our_first_db_copy CLONE our_first_db;
```
- This creates `our_first_db_copy`, a duplicate of `our_first_db`.
- All schemas, tables, and objects are cloned.

### Step 2: Verifying the Cloned Database
After execution:

```sql
SHOW DATABASES;
```
- `our_first_db_copy` should appear in the list.
- It contains:
  - `copied_schema`
  - `external_stages_copied`
  - All original objects from `our_first_db`.

## 5. Cleaning Up Cloned Objects
To avoid unnecessary storage and clutter, cloned databases and schemas can be removed.

### Deleting a Cloned Database
```sql
DROP DATABASE our_first_db_copy;
```

### Deleting a Cloned Schema
```sql
DROP SCHEMA our_first_db.copied_schema;
```

## 6. Key Considerations
| Cloning Level  | What is Copied?                    | What is NOT Copied?                  |
|---------------|---------------------------------|--------------------------------|
| **Table**     | Structure, Data (zero-copy)    | Constraints, Triggers, Grants |
| **Schema**    | All Tables, Views, Stages     | Snowflake Streams, Some External Links |
| **Database**  | All Schemas, Tables, Stages   | External Integrations |

## 7. Conclusion
- **Cloning Schemas**: Duplicates schema and tables within or across databases.
- **Cloning Databases**: Copies an entire database, including all schemas and objects.
- **Zero-Copy Efficiency**: Cloning is fast and storage-friendly, only consuming space when modifications are made.
- **Temporary and Transient Objects**: Best practice is to use transient schemas and databases to avoid unnecessary fail-safe storage.

This feature is powerful for backup, testing, and environment replication in Snowflake.
