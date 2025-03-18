
## Snowflake Architecture
Snowflake integrates aspects of both shared-disk and shared-nothing architectures:
- **Shared-disk:** Centralized data repository accessible by all compute nodes.
- **Shared-nothing:** Massively parallel processing (MPP) with distributed compute clusters.
- Provides both the simplicity of shared-disk and the scalability of shared-nothing architectures.

### Three Key Layers of Architecture
#### 1. Database Storage
- Data is optimized, compressed, and stored in Snowflake’s internal format.
- Fully managed storage handling file organization, compression, metadata, and statistics.
- Data access is only available through SQL queries.

#### 2. Query Processing
- Queries are executed via **virtual warehouses**, which are independent MPP clusters.
- Each virtual warehouse operates separately, ensuring no performance impact on others.

#### 3. Cloud Services
Manages overall coordination within Snowflake, including:
- Authentication & Access Control
- Infrastructure & Metadata Management
- Query Parsing & Optimization

