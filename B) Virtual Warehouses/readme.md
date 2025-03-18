
# Warehouses in Snowflake
Warehouses are required for query execution and DML operations, including data loading. They can be dynamically resized and automatically started or suspended.

## Warehouse Types
### Standard Warehouses
- Used for general SQL workloads.
- Support resizing, auto-suspend, and auto-resume.
- Suitable for transactional and analytical processing.

### Snowpark Warehouses
- Optimized for Python-based Snowpark workloads.
- Ensure efficient execution of machine learning and AI models.
- Provide separate compute resources for Python execution to optimize cost and performance.

## Multi-Cluster Warehouses
Multi-cluster warehouses automatically scale based on query concurrency. They are useful for high-demand scenarios, such as large-scale reporting or real-time analytics.

- **Min and Max Clusters:** Define the range of clusters that can be active.
- **Scaling Policy:**
  - **Standard:** Adds clusters only when queries are queued.
  - **Economy:** Adds clusters more conservatively to optimize cost.
- **Auto-Suspension:** Multi-cluster warehouses suspend only when the minimum cluster count is reached.

## Warehouse Size & Credit Usage
| Warehouse Size | Credits/Hour | Notes |
|---------------|-------------|-------|
| X-Small      | 1           | Default for Snowsight & CREATE WAREHOUSE |
| Small        | 2           | |
| Medium       | 4           | |
| Large        | 8           | |
| X-Large      | 16          | Default for Classic Console |
| 2X-Large     | 32          | |
| 3X-Large     | 64          | |
| 4X-Large     | 128         | |
| 5X-Large     | 256         | Available in AWS & Azure, preview in US Gov regions |
| 6X-Large     | 512         | Available in AWS & Azure, preview in US Gov regions |

## Auto-Suspension & Auto-Resumption
- **Auto-Suspend:** Suspends inactive warehouses to save credits.
- **Auto-Resume:** Restarts warehouses when queries are submitted.

## Query Processing & Concurrency
- Query performance scales with warehouse size.
- Large queries benefit from larger warehouses, but small queries may not.
- Queries exceeding resource limits are queued until resources are available.
- Multi-cluster warehouses enable automated scaling for concurrency.

## Warehouse Usage in Sessions
- A session does not have a default warehouse unless explicitly set.
- Users can have a **default warehouse** assigned for automatic usage.
- Clients (SnowSQL, JDBC, ODBC) can specify default warehouses.

## Cost Management Recommendations
- Use **SYSTEM$STREAMLIT_NOTEBOOK_WH** for Streamlit and Notebook workloads.
- Separate Python workloads from SQL queries to reduce costs.
- Default warehouse precedence: User setting → Client configuration → Command-line parameter.

For more details, refer to the [Snowflake Documentation](https://docs.snowflake.com/).

