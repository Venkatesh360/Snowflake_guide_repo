# Sampling in Snowflake

## Query Syntax: SAMPLE / TABLESAMPLE

Snowflake provides the `SAMPLE` and `TABLESAMPLE` clauses to retrieve a randomly selected subset of rows from a table. These clauses can be used interchangeably.

### Sampling Methods

Snowflake supports two main types of sampling:

1. **Fraction-based Sampling:**
   - Selects a percentage of rows from the table.
   - The number of rows returned depends on the table size and specified probability.
   - A seed can be provided to make results deterministic.

2. **Fixed-size Sampling:**
   - Returns a specified number of rows.
   - If the table contains fewer rows than requested, the entire table is returned.

### Syntax

```sql
SELECT ...
FROM ...
  { SAMPLE | TABLESAMPLE } [ samplingMethod ]
[ ... ]
```

Where:

```sql
samplingMethod ::= { { BERNOULLI | ROW } ( { <probability> | <num> ROWS } ) |
                     { SYSTEM | BLOCK } ( <probability> ) [ { REPEATABLE | SEED } ( <seed> ) ] }
```

### Parameters

#### Sampling Methods
- **BERNOULLI (ROW):** Includes each row with a probability of `p/100`.
- **SYSTEM (BLOCK):** Includes each block of rows with a probability of `p/100`.
- The default method is `BERNOULLI`.

#### Probability and Row Count
- **`<probability>`**: A percentage (0–100) determining the likelihood of selecting a row.
- **`<num> ROWS`**: The exact number of rows to sample (up to 1,000,000).

#### Seeding for Deterministic Results
- **`REPEATABLE | SEED (seed)`**: Ensures the same sample is returned when re-running a query.
- Only applicable for `SYSTEM | BLOCK` sampling.
- Seed values range from 0 to 2,147,483,647.

## Usage Notes

- The following keywords are interchangeable:
  - `SAMPLE | TABLESAMPLE`
  - `BERNOULLI | ROW`
  - `SYSTEM | BLOCK`
  - `REPEATABLE | SEED`

- For large tables, differences between `BERNOULLI` and `SYSTEM` sampling are negligible.
- Without a seed, each execution of `SAMPLE` may return different results.
- `SEED (seed)` is not supported for:
  - Fixed-size sampling
  - Views or subqueries

## Performance Considerations

- `SYSTEM | BLOCK` sampling is usually faster than `BERNOULLI | ROW` sampling.
- Sampling without a seed is faster than with a seed.
- Fixed-size sampling can be slower than fraction-based sampling.

## Examples

### Fraction-Based Row Sampling

```sql
SELECT * FROM testtable SAMPLE (10); -- 10% of rows
SELECT * FROM testtable TABLESAMPLE BERNOULLI (20.3); -- 20.3% of rows
SELECT * FROM testtable TABLESAMPLE (100); -- All rows (full table)
SELECT * FROM testtable SAMPLE ROW (0); -- No rows
```

### Sampling with Joins

```sql
SELECT i, j
FROM table1 AS t1 SAMPLE (25)
INNER JOIN table2 AS t2 SAMPLE (50)
ON t2.j = t1.i;
```

### Block Sampling with Seeds

```sql
SELECT * FROM testtable SAMPLE SYSTEM (3) SEED (82);
SELECT * FROM testtable SAMPLE BLOCK (0.012) REPEATABLE (99992);
```

### Fixed-Size Sampling

```sql
SELECT * FROM testtable SAMPLE (10 ROWS);
```

By using the `SAMPLE` clause effectively, users can efficiently retrieve subsets of data for analysis and testing in Snowflake.