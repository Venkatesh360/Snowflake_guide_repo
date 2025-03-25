# Changes Clause in Snowflake

## Introduction
The `CHANGES` clause in Snowflake provides a way to track changes in a table dynamically, similar to streams, but with more manual control over the offset. Unlike streams, which automatically reset upon consumption, the `CHANGES` clause allows users to define offsets explicitly using timestamps.

## Enabling Change Tracking
To use the `CHANGES` clause, change tracking must be enabled on the table. This can be done using the following command:

```sql
ALTER TABLE Sales_Raw SET CHANGE_TRACKING = TRUE;
```

If a stream already exists on the table, change tracking is automatically enabled.

## Using the CHANGES Clause
The `CHANGES` clause is used in a `SELECT` statement to track changes over time within the time travel period.

### Example Setup
```sql
CREATE DATABASE sales_db;
CREATE TABLE Sales_Raw (id INT, product STRING);
INSERT INTO Sales_Raw VALUES (1, 'Apple'), (2, 'Orange');
```

### Querying Changes
To track changes from a specific point in time:

```sql
SELECT * FROM Sales_Raw CHANGES(SINCE => '2024-03-25 12:00:00');
```

### Default vs. Append-Only Mode
- **Default mode** tracks inserts, updates, and deletes.
- **Append-only mode** tracks only inserts.

#### Append-Only Example
```sql
SELECT * FROM Sales_Raw CHANGES(SINCE => '2024-03-25 12:00:00', APPEND_ONLY => TRUE);
```

## Behavior of CHANGES Clause
- The clause works within the time travel period.
- Changes are tracked based on timestamps.
- Unlike streams, the offset is not reset automatically.

## Practical Usage
1. **Track new inserts dynamically** using append-only mode.
2. **Manually control change tracking** for analytical queries.
3. **Compare historical data** by adjusting the offset timestamps.

## Conclusion
The `CHANGES` clause provides an alternative to streams for tracking table modifications with more flexibility in defining the offset. However, for automated workflows, streams remain the preferred choice.

