# Data Sampling in Snowflake

## 1. What is Data Sampling?
Data sampling is a technique used to retrieve a subset of rows from a table instead of querying the entire dataset. This is useful when working with large tables to improve performance and reduce query costs.

## 2. Sampling Methods in Snowflake
Snowflake provides the `TABLESAMPLE` clause, which supports three sampling methods:

- **Bernoulli Sampling (BERNOULLI)**
- **System Sampling (SYSTEM)**
- **Row-Based Sampling (LIMIT)**

## 3. Bernoulli Sampling (BERNOULLI)
- Uses a probabilistic approach, meaning each row has an independent chance of being selected.
- More random distribution, but may result in variable row counts across runs.
- Best for scenarios where even distribution is required.

### Example:
```sql
SELECT * FROM customers TABLESAMPLE BERNOULLI (10);
```
✅ Retrieves approximately 10% of rows from the `customers` table.

## 4. System Sampling (SYSTEM)
- Uses block-level sampling (Snowflake’s internal storage blocks).
- Faster than `BERNOULLI` but may introduce bias (some blocks might have more data than others).
- Best for large datasets when performance is more important than precision.

### Example:
```sql
SELECT * FROM customers TABLESAMPLE SYSTEM (10);
```
✅ Retrieves approximately 10% of the table but may not be as evenly distributed as `BERNOULLI`.

## 5. Row-Based Sampling (LIMIT)
- Retrieves a fixed number of rows.
- Does not guarantee randomness, as it selects rows based on the table's order.
- Best for quick previews of data.

### Example:
```sql
SELECT * FROM customers LIMIT 100;
```
✅ Returns exactly 100 rows but may not be randomly distributed.

## 6. Comparison of Sampling Methods
| Sampling Method | How It Works | Performance | Best Use Case |
|----------------|-------------|------------|---------------|
| BERNOULLI | Random row selection | ❌ Slower | Evenly distributed random samples |
| SYSTEM | Selects entire storage blocks | ✅ Faster | Large datasets, approximate results |
| LIMIT | Retrieves fixed number of rows | ✅ Fastest | Quick data previews |

## 7. Using Sampling with a WHERE Clause
You can combine `TABLESAMPLE` with a `WHERE` clause for filtered sampling.

### Example:
```sql
SELECT * FROM customers 
TABLESAMPLE BERNOULLI (10)
WHERE country = 'USA';
```
✅ Retrieves 10% of U.S. customers from the table.

## 8. Use Cases for Data Sampling
- **Testing Queries** → Run queries on a small sample before running on the full dataset.
- **Performance Optimization** → Reduce query execution time and costs.
- **Exploratory Data Analysis (EDA)** → Get a quick overview of a dataset.
- **Machine Learning & Analytics** → Train models on a representative dataset before scaling.

## 9. Summary
| Sampling Method | When to Use |
|----------------|------------|
| BERNOULLI | Need truly random sampling |
| SYSTEM | Large tables where performance matters |
| LIMIT | Quick preview of a fixed number of rows |
