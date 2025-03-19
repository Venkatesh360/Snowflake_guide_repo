# Time Travel in Snowflake

## **What is Time Travel?**
Snowflake **Time Travel** allows you to **query, restore, or clone** historical data from a **table, schema, or database** for a specific period after changes or deletions.  
It is useful for **recovering deleted data, auditing changes, and debugging.**

---

## **1️⃣ How Long Can You Time Travel?**
The retention period for Time Travel depends on the **table type**:

| Table Type  | Default Retention | Max Retention |
|-------------|------------------|--------------|
| **Permanent** | 1 day | Up to 90 days* |
| **Transient** | 0 days (disabled) | Up to 1 day |
| **Temporary** | 0 days (disabled) | Not Supported |

> *Maximum retention depends on your Snowflake edition (Standard: 1 day, Enterprise: 90 days).*

---

## **2️⃣ Using Time Travel**

### ✅ **1. Query Historical Data (AT | BEFORE)**
You can query past versions of a table using **timestamps, offsets, or statement IDs**.

```sql
SELECT * FROM orders AT (TIMESTAMP => '2024-03-10 12:00:00');
SELECT * FROM orders BEFORE (STATEMENT => '8d4e1bcd-1234-5678-9abc-def012345678');
```

🔹 **Options:**  
- **TIMESTAMP** → Retrieve data at a specific time.  
- **OFFSET** → Retrieve data N seconds before the current time.  
- **STATEMENT** → Retrieve data before a specific query execution.  

---

✅ **2. Restore a Dropped Table (UNDROP TABLE)**  
If a table is accidentally dropped, you can recover it:  

```sql
UNDROP TABLE orders;
```

🔹 Works only within the retention period.

✅ 3. **Clone a Historical Table (CLONE)**
You can clone a past version of a table for testing or rollback purposes:

```sql
CREATE TABLE orders_backup CLONE orders AT (TIMESTAMP => '2024-03-10 12:00:00');
```
🔹 This creates a copy of the table as it existed at that time.
## **3️⃣ When is Time Travel Useful?**
- ✔ Accidental data deletion recovery (UNDROP TABLE).
- ✔ Analyzing past data states (SELECT * AT TIMESTAMP).
- ✔ Cloning historical snapshots for reporting or debugging.
- ✔ Auditing and compliance by reviewing past data changes.

## **4️⃣ Failsafe vs. Time Travel**
| Feature     | Time Travel                         | Failsafe                              |
|------------|----------------------------------|----------------------------------|
| **Purpose** | User-controlled recovery        | Snowflake-managed disaster recovery |
| **Retention** | Up to 90 days (Enterprise)      | Additional 7 days after Time Travel expires |
| **Usage**   | Query, restore, clone          | Requires Snowflake support request  |
| **Performance** | Instant access                  | Slower, used for extreme cases      |

## 🚀 **Summary**  
✔ Time Travel lets you query, restore, or clone historical data.  
✔ Supports querying data using timestamps, offsets, or statement IDs.  
✔ Use `UNDROP TABLE` to restore dropped tables.  
✔ Use `CLONE` to create a snapshot of past data.  
✔ Works within the retention period (up to 90 days on Enterprise).  
