# Effect of Warehouse Suspension on Cache in Snowflake

When a virtual warehouse in Snowflake is suspended, it affects caching in the following ways:

| Cache Type       | Effect of Warehouse Suspension |
|------------------|--------------------------------|
| **Result Cache**   | ✅ Unaffected – Stored at the Snowflake service layer, persists for 24 hours. |
| **Metadata Cache** | ✅ Unaffected – Stored at the Snowflake service layer, persists until metadata changes. |
| **Warehouse Cache** | ❌ Cleared – Local disk cache (SSD storage) is lost when the warehouse is suspended. |

## Detailed Impact on Each Cache Type

### Result Cache (✅ Unaffected)

Since this cache is managed at the Snowflake service layer, it is not tied to a specific warehouse.

Even if the warehouse is suspended, queries with identical SQL can still retrieve results from the Result Cache as long as:

- The query was executed in the past 24 hours.
- The underlying data has not changed.

**Example:**

```sql
SELECT COUNT(*) FROM sales WHERE region = 'North America';
```

If this query was executed earlier and the result is cached, suspending the warehouse does not affect its availability.

### Metadata Cache (✅ Unaffected)

Metadata about tables (such as micro-partition pruning information, statistics, and query execution plans) is stored at the Snowflake service layer.

Since it does not depend on a specific warehouse, suspending a warehouse has no impact on Metadata Cache.

**Example:**

If a query filters data on a clustered column, Snowflake can prune unnecessary partitions even after the warehouse is restarted.

### Warehouse Cache (❌ Cleared)

This cache stores compressed micro-partitions of table data on local SSD storage attached to the warehouse.

When a warehouse is suspended, all local SSD storage is released, causing the Warehouse Cache to be lost.

When the warehouse is resumed:

- Snowflake must fetch data from remote storage (S3 or Azure Blob) again.
- This results in higher query latency and increased compute costs due to additional I/O operations.

## Example Scenario: Warehouse Suspension Impact

### Step 1: Running Queries with Warehouse Cache

```sql
SELECT * FROM orders WHERE order_date >= '2024-01-01';
```

The warehouse fetches data from storage and caches it in Warehouse Cache (local SSD).

### Step 2: Suspended Warehouse

```sql
ALTER WAREHOUSE my_wh SUSPEND;
```

The Warehouse Cache is cleared.

### Step 3: Resuming and Re-running Query

```sql
ALTER WAREHOUSE my_wh RESUME;

SELECT * FROM orders WHERE order_date >= '2024-01-01';
```

Since Warehouse Cache was lost, Snowflake must fetch data from remote storage again, leading to higher execution time.

## Best Practices to Preserve Performance After Suspension

- Keep warehouses running if frequent queries depend on Warehouse Cache.
- Use larger warehouse sizes to optimize data retrieval from remote storage.
- Utilize **clustering keys** to improve query pruning efficiency.
- Leverage **Materialized Views** for frequently accessed datasets.
