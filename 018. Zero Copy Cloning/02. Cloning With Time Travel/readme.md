# Using Cloning with Time Travel & SWAP in Snowflake

Snowflake’s Time Travel and Zero-Copy Cloning are powerful features for recovering from accidental changes or deletions. When combined with the SWAP command, they provide an efficient way to restore data while minimizing downtime.

## 1. Overview of Time Travel, Cloning, and SWAP

| Feature          | Purpose                                                     |
|-----------------|-------------------------------------------------------------|
| Time Travel     | Allows accessing past versions of data (up to 90 days).    |
| Zero-Copy Cloning | Creates an instant duplicate without physical storage duplication. |
| SWAP            | Exchanges two tables, schemas, or databases instantly.     |

By cloning an object from a past state and then swapping it with the current version, we can recover from mistakes efficiently.

## 2. Cloning a Table Using Time Travel

If a table was mistakenly updated or deleted, we can clone it at a previous timestamp.

### Syntax: Cloning a Table at a Past Time
```sql
CREATE TABLE cloned_table CLONE original_table AT (TIMESTAMP => '<timestamp>');
```
OR
```sql
CREATE TABLE cloned_table CLONE original_table AT (OFFSET => -<seconds>);
```

### Example: Recovering from an Accidental Update
If an update mistakenly set all `last_name` values to `NULL`, we can recover the table:
```sql
CREATE TABLE customers_backup CLONE customers AT (TIMESTAMP => '2025-03-20 14:00:00');
```
This instantly restores the `customers` table as it was at 2:00 PM on March 20, 2025.

## 3. Restoring a Dropped Table Using Time Travel

If a table was accidentally dropped, it can still be recovered within the Time Travel retention period.

### Syntax: Restoring a Dropped Table
```sql
CREATE TABLE recovered_table CLONE DROPPED TABLE original_table AT (BEFORE DROP);
```

### Example: Recovering a Dropped Table
```sql
CREATE TABLE customers_restored CLONE DROPPED TABLE customers AT (BEFORE DROP);
```
This brings back the `customers` table as it was before deletion.

## 4. Using SWAP to Restore a Table Seamlessly

After cloning a table from a previous timestamp, the final step is swapping it with the current version. This avoids manually renaming and ensures minimal downtime.

### Syntax: SWAP Tables
```sql
ALTER TABLE original_table SWAP WITH cloned_table;
```

### Example: Swapping a Cloned Table with the Original
```sql
ALTER TABLE customers SWAP WITH customers_backup;
```
The original table (`customers`) now contains the recovered data.
The backup table (`customers_backup`) holds the incorrect version (which can be dropped if no longer needed).

## 5. Cloning & Swapping Schemas
Schemas can also be cloned at a previous timestamp and swapped with the current version.

### Syntax: Cloning a Schema Using Time Travel
```sql
CREATE SCHEMA schema_backup CLONE original_schema AT (OFFSET => -3600);
```
(This clones the schema as it was one hour ago.)

### Swapping the Schema
```sql
ALTER SCHEMA original_schema SWAP WITH schema_backup;
```
The original schema is instantly restored without affecting table references.

## 6. Cloning & Swapping Databases

For large-scale rollbacks, an entire database can be cloned and swapped.

### Syntax: Cloning a Database
```sql
CREATE DATABASE db_backup CLONE original_db AT (BEFORE DROP);
```

### Swapping the Database
```sql
ALTER DATABASE original_db SWAP WITH db_backup;
```
This restores the database while keeping existing connections intact.

## 7. Benefits of Cloning + SWAP

✅ **Instantaneous Recovery** – No need to manually restore data.  
✅ **Zero Downtime** – SWAP ensures seamless transitions.  
✅ **Efficient Storage** – No duplication costs unless modifications occur.  
✅ **Flexible Rollbacks** – Recover tables, schemas, or entire databases.  

## 8. Summary

- Use **Time Travel** to clone a table/schema/database at a previous state.
- If necessary, recover deleted objects using `AT (BEFORE DROP)`.
- Use **SWAP** to replace the current version with the recovered version instantly.
- Drop the backup object if no longer needed.

By leveraging **Time Travel, Cloning, and SWAP**, Snowflake provides a robust, fast, and storage-efficient way to recover from errors.
