# Materialized Views in Snowflake

## 1️⃣ What is a Materialized View (MV)?
A Materialized View (MV) in Snowflake is a precomputed, stored query result that improves query performance by avoiding repetitive computation. Unlike standard views, which execute the query every time they are accessed, materialized views store the computed results and update them as data changes.

## 2️⃣ How to Create a Materialized View?

### ✅ Creating a Materialized View
```sql
CREATE MATERIALIZED VIEW mv_sales AS 
SELECT customer_id, SUM(total_amount) AS total_spent 
FROM sales 
GROUP BY customer_id;
```
🔹 This precomputes the total amount spent by each customer from the sales table.
🔹 Future queries on `mv_sales` will run faster than querying `sales` directly.

## 3️⃣ How Does a Materialized View Update?
Snowflake automatically refreshes materialized views incrementally when data in the base table changes.

Updates happen asynchronously in the background using a compute resource called a "maintenance service."

## 4️⃣ Cost Considerations
Materialized Views incur additional costs due to:

| Cost Component  | Description |
|---------------|-------------|
| **Storage Cost** | MVs store precomputed results, consuming additional storage. |
| **Compute Cost** | Snowflake automatically updates MVs when the base table changes, using compute resources. |
| **Query Cost** | Queries on MVs are faster but require extra storage and maintenance costs. |

💰 **Example: Cost Consideration**  
If you create an MV on a large table with frequent updates, Snowflake must recompute the MV often, increasing compute usage and costs.

## 5️⃣ Limitations of Materialized Views

| Limitation | Description |
|------------|-------------|
| **No Joins Allowed** | MVs cannot include `JOIN` clauses. |
| **No Aggregates with DISTINCT** | Queries with `SUM(DISTINCT col)` are not allowed. |
| **No Non-Deterministic Functions** | Functions like `CURRENT_TIMESTAMP`, `RAND()` are not supported. |
| **Limited Expressions** | Cannot use subqueries, `HAVING`, or `UNION` in an MV. |
| **Compute Cost on Updates** | MVs increase compute usage since Snowflake must maintain them when the base table changes. |

## 6️⃣ When to Use Materialized Views?
✅ **Frequent Aggregations** – e.g., total sales per month.  
✅ **Precomputing Expensive Queries** – e.g., filtering data subsets.  
✅ **Performance Optimization** – speeding up repetitive queries.  

## 7️⃣ When NOT to Use Materialized Views?
❌ When data updates frequently → Can lead to high compute costs.  
❌ If you need joins or complex expressions → Not supported.  
❌ For ad-hoc queries → Regular views are better since they don’t require storage.  

## 🚀 Summary
✔ Materialized Views store precomputed query results for faster performance.  
✔ They are automatically updated but at an additional storage and compute cost.  
✔ They have limitations (e.g., no joins, no subqueries).  
✔ Best for queries with frequent aggregations or filters on large datasets.

