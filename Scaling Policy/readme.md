
# Scaling Policy in Snowflake

Scaling policies in Snowflake determine how virtual warehouses add or remove compute clusters based on workload demand. This feature is particularly useful for multi-cluster warehouses to optimize cost and performance.

## Scaling Policies
Snowflake provides two primary scaling policies:

### 1. Standard Scaling
- Clusters are added **only when queries are queued** due to insufficient compute resources.
- Ensures rapid response to demand but may cause slight delays while clusters start.
- Best suited for workloads with fluctuating demand but strict performance requirements.

### 2. Economy Scaling
- Clusters are added **more conservatively** to minimize credit consumption.
- Prioritizes cost savings over immediate performance.
- Ideal for workloads where slight delays are acceptable to reduce costs.

## Multi-Cluster Warehouse Scaling
When using a **multi-cluster warehouse**, the following parameters affect scaling behavior:
- **Min and Max Clusters:** Defines the range of clusters available for scaling.
- **Auto-Suspension:** Active clusters suspend individually, but only down to the minimum set cluster count.
- **Auto-Resume:** Additional clusters are brought online as query load increases.

## Choosing the Right Scaling Policy
- Use **Standard Scaling** when performance and low query latency are priorities.
- Use **Economy Scaling** for predictable workloads where cost savings outweigh performance considerations.
