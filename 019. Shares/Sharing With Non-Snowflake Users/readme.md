# Sharing Data with Non-Snowflake Users

Snowflake provides Secure Data Sharing primarily between Snowflake accounts, but it also allows sharing data with non-Snowflake users using Reader Accounts or Snowflake Data Marketplace.

## 1. Methods to Share Data with Non-Snowflake Users

| Method                     | Best For                           | How It Works |
|----------------------------|-----------------------------------|--------------|
| **Reader Accounts**        | External users without Snowflake  | Snowflake provides a limited Snowflake account where users can query shared data. |
| **Snowflake Data Marketplace** | Public or commercial data sharing | Users access data via Snowflake’s marketplace without needing a separate Snowflake account. |
| **External Cloud Storage**  | Users on AWS, Azure, GCP         | Share data via S3, Blob Storage, or Google Cloud Storage. |
| **APIs and Web Services**   | Applications & real-time integration | Expose Snowflake data through RESTful APIs. |
| **Exporting Data (CSV, Parquet, etc.)** | Traditional file-based sharing | Export and share files manually via email or storage services. |

## 2. Using Reader Accounts

Reader Accounts allow non-Snowflake users to access shared data via Snowflake’s UI or SQL queries without needing their own Snowflake subscription.

### 2.1 Creating a Reader Account
```sql
CREATE MANAGED ACCOUNT my_reader_account
ADMIN_NAME = 'reader_admin'
ADMIN_PASSWORD = 'StrongPassword123'
TYPE = READER;
```

### 2.2 Granting Access to a Reader Account
```sql
GRANT USAGE ON DATABASE shared_db TO SHARE my_data_share;
ALTER SHARE my_data_share ADD ACCOUNT = 'my_reader_account';
```

### 2.3 Non-Snowflake User Access
- The user logs into the Reader Account via Snowflake’s web UI.
- They can run SQL queries but cannot store or edit data.
- Reader Accounts are free, but the provider pays for compute costs.

## 3. Using Snowflake Data Marketplace

The Snowflake Data Marketplace allows data providers to share data publicly or privately without requiring consumers to manage Snowflake accounts.

### How It Works
- The provider publishes datasets on the Marketplace.
- Consumers request access via the Snowflake UI.
- Data is read-only and always up to date.

✅ Best for data monetization, public datasets, or partner collaboration.

## 4. Sharing Data via External Cloud Storage

### 4.1 Exporting Data to AWS S3, Azure Blob, or GCP Storage
```sql
COPY INTO 's3://my-bucket/data.csv'
FROM my_table
FILE_FORMAT = (TYPE = CSV);
```
- The external user accesses the file via S3, Azure Blob, or Google Cloud Storage.
- Requires additional security settings like signed URLs or IAM roles.

## 5. Exposing Snowflake Data via APIs

For real-time access, Snowflake data can be exposed through REST APIs.

### 5.1 Using Snowflake’s API Integration
```sql
CREATE API INTEGRATION my_api_integration
API_PROVIDER = aws_api_gateway
ENABLED = TRUE;
```
- Non-Snowflake users can access data via secure API endpoints.
- Best for integrating with web apps, external systems, or BI tools.

## 6. Exporting Data in File Formats (CSV, Parquet, JSON)

Non-Snowflake users can receive exported data in standard formats.

### 6.1 Exporting Data to CSV
```sql
COPY INTO @my_stage/data.csv
FROM my_table
FILE_FORMAT = (TYPE = CSV);
```
- Users download the file and use tools like Excel or SQL databases.
- This method lacks real-time updates and requires manual exports.

## 7. Choosing the Right Method for Sharing Data

| Use Case                        | Recommended Sharing Method |
|----------------------------------|---------------------------|
| Internal Teams (Same Org)        | Secure Data Sharing (Shares) |
| External Partners (with Snowflake) | Secure Data Sharing (Shares) |
| External Users (No Snowflake)    | Reader Accounts |
| Monetizing Data                  | Snowflake Data Marketplace |
| Cloud-Based Sharing              | AWS S3, Azure Blob, GCP Storage |
| Real-Time API Access             | REST API or Web Services |
| One-Time Data Exports            | CSV, Parquet, JSON exports |

## 8. Summary

- **Reader Accounts** allow external users to query data without owning a Snowflake account.
- **Snowflake Marketplace** provides public & private data-sharing options.
- **Cloud storage & APIs** enable integration with non-Snowflake users.
- **File exports (CSV, JSON, Parquet)** provide an easy but manual sharing method.
