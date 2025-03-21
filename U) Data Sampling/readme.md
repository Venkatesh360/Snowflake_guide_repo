Sampling in Snowflake

## What is Sampling?
Sampling is a technique used to retrieve a subset of data from a table instead of scanning the entire dataset. This helps optimize performance, reduce query costs, and allow quick data analysis without processing all rows.

Snowflake provides the `TABLESAMPLE` clause, which allows users to select a random subset of rows from a table.

## How Sampling Works in Snowflake
When a sampling query is executed, Snowflake applies probabilistic methods to choose a subset of rows from the dataset based on the specified percentage. However, the way rows are selected depends on the sampling method chosen.

## Types of Sampling Methods in Snowflake
Snowflake provides two main types of sampling:

### 1. BERNOULLI Sampling (Row-Level Sampling - True Random Sampling)
**How it works:**

- Each row in the table is evaluated independently.
- Each row has an equal probability of being selected.
- The selected rows are randomly distributed across the table.

**Query Syntax:**
```sql
SELECT * FROM employees TABLESAMPLE (BERNOULLI, 10);
```
This selects approximately 10% of the total rows in the `employees` table.

**Characteristics:**

- Slower than SYSTEM sampling because each row is processed individually.
- Useful for cases where a truly random subset is needed.
- Results may vary if the query is executed multiple times.

### 2. SYSTEM Sampling (Block-Level Sampling - Faster but Less Random)
**How it works:**

- Snowflake randomly selects micro-partitions instead of individual rows.
- A micro-partition is a physical storage unit in Snowflake containing multiple rows.
- If a partition is selected, all rows in that partition are included in the result.

**Query Syntax:**
```sql
SELECT * FROM employees TABLESAMPLE (SYSTEM, 10);
```
This selects approximately 10% of the micro-partitions, which can result in more or fewer than 10% of rows, depending on how the data is stored.

**Characteristics:**

- Much faster than BERNOULLI sampling because it selects entire blocks of data.
- Less random because all rows in a selected partition are included.
- Suitable for performance-sensitive queries when full randomness is not required.

## Ensuring Consistent Sampling Results
By default, Snowflake’s sampling methods return different results each time they are run. To make sampling repeatable, you can use the `REPEATABLE(seed)` option.

**Query Syntax:**
```sql
SELECT * FROM employees TABLESAMPLE (BERNOULLI, 10) REPEATABLE(123);
```
- The `123` is a seed value that ensures the same sample is selected each time the query is executed.
- Different seed values will produce different samples.

## Key Differences Between BERNOULLI and SYSTEM Sampling
| Feature | BERNOULLI Sampling (Row-Level) | SYSTEM Sampling (Partition-Level) |
|---------|--------------------------------|-----------------------------------|
| Selection Process | Evaluates each row independently | Selects entire micro-partitions |
| Randomness | High (truly random) | Less random (depends on partitions) |
| Performance | Slower (evaluates each row) | Faster (reads partition blocks) |
| Reproducibility | Different results per execution (unless using REPEATABLE) | Different results per execution (unless using REPEATABLE) |
| Best Use Case | When you need true randomness | When you need faster sampling |

## Can the Same Record Be Chosen More Than Once?
No, Snowflake sampling is done **without replacement**.

- Each row is either selected once or not at all in a single execution.
- Unlike methods such as bootstrapping in statistics (which allow duplication), Snowflake does not select the same row more than once.

## When to Use Sampling in Snowflake?

### 1. Exploratory Data Analysis (EDA)
If you have a large dataset, sampling allows you to quickly inspect data trends.

**Example:** Get a quick preview of customer transactions.
```sql
SELECT * FROM transactions TABLESAMPLE (BERNOULLI, 5);
```

### 2. Machine Learning & Model Training
Use sampling to train models on a subset of data rather than the full dataset.

**Example:** Get 20% of user activity data for model training.
```sql
SELECT * FROM user_activity TABLESAMPLE (SYSTEM, 20);
```

### 3. Testing Queries on a Smaller Dataset
Before running a complex query on millions of rows, test it on a smaller sample.

**Example:** Debugging an aggregation query.
```sql
SELECT category, AVG(price) 
FROM products TABLESAMPLE (BERNOULLI, 10) 
GROUP BY category;
```

### 4. Performance Optimization
Sampling allows you to run analytics queries faster by reducing the number of scanned rows.

## Conclusion
Snowflake’s `TABLESAMPLE` provides a powerful way to extract subsets of data for analysis.

- Use **BERNOULLI** for true randomness but expect slightly slower performance.
- Use **SYSTEM** for faster queries but be aware that entire micro-partitions are selected.
- Use **REPEATABLE(seed)** to get the same results across multiple executions.
