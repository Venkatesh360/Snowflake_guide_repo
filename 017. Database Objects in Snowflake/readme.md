# Comparison of Permanent, Transient, and Temporary Database Objects in Snowflake

| Feature        | Permanent Objects | Transient Objects | Temporary Objects |
|---------------|------------------|------------------|------------------|
| **Persistence** | Persist indefinitely until explicitly dropped | Persist across sessions but lack Fail-Safe | Exist only during the session |
| **Session Scope** | Available across all sessions | Available across all sessions | Available only in the session where created |
| **Fail-Safe** | ✅ Enabled (7 days for Standard, 90 days for Enterprise) | ❌ Not available | ❌ Not available |
| **Time Travel** | ✅ Enabled (Default: 1 day, up to 90 days for Enterprise) | ⏳ Can be set (Default: 0 days) | ❌ Not available |
| **Sharing** | ✅ Can be shared across users and sessions | ✅ Can be shared across users and sessions | ❌ Cannot be shared between sessions |
| **Performance** | Standard storage and performance | Lower storage cost, better for temporary data | Faster performance due to session scope |
| **Storage Cost** | High (due to Fail-Safe and Time Travel) | Lower (no Fail-Safe, limited Time Travel) | Lowest (data is session-based) |
| **Best Used For** | Critical business data, production tables, historical records | Staging, frequently updated data, ETL intermediate storage | Temporary queries, session-based processing, short-term ETL steps |

## How Transient Schema Affects Created Objects
A transient schema impacts objects created within it by inheriting its transient nature:

- Transient tables, views, and stages within a transient schema also lack Fail-Safe and have limited Time Travel (default: 0 days).
- Permanent tables in a transient schema will still follow permanent table rules, meaning they will retain Fail-Safe and Time Travel.
- If a transient schema is dropped, its objects are permanently deleted with no recovery options.

### Example:
```sql
CREATE TRANSIENT SCHEMA my_transient_schema;
USE my_transient_schema;

-- Creating a transient table within the transient schema
CREATE TRANSIENT TABLE my_table (
    id INT PRIMARY KEY,
    name STRING
);

-- Creating a permanent table inside a transient schema (it remains permanent)
CREATE TABLE my_permanent_table (
    id INT PRIMARY KEY,
    data STRING
);
```
- `my_table` is transient (no Fail-Safe, minimal Time Travel).
- `my_permanent_table` remains permanent despite being in a transient schema.

