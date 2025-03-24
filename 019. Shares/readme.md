# Shares in Snowflake: Secure Data Sharing Explained

## 1. Overview of Snowflake Shares
Snowflake’s Secure Data Sharing allows organizations to share live data with external or internal users without copying or moving data. This is done using Shares, which provide read-only access to databases, schemas, and tables.

## 2. Key Features of Secure Data Sharing
- **Zero Copy** – No data duplication; shared data remains in the provider's account.
- **Real-Time Access** – Consumers always access the most recent data.
- **Granular Control** – Share entire databases, schemas, or specific tables.
- **No Additional Cost** – Consumers only pay for queries, not storage.

## 3. How Snowflake Shares Work
### 1. Data Provider (Owner)
- Owns the data and creates a Share.
- Grants read-only access to specific objects.
- Sends an invitation to the Consumer.

### 2. Data Consumer
- Accepts the share using their Snowflake account.
- Queries the data without storing or copying it.
- Cannot modify the shared data (only read access).

## 4. Creating and Managing Shares
### 4.1 Creating a Share
The data provider uses the `CREATE SHARE` command to share data.

```sql
CREATE SHARE sales_data_share;
```

### 4.2 Granting Access to a Database, Schema, or Table
After creating the share, grant access to specific objects.

```sql
GRANT USAGE ON DATABASE sales_db TO SHARE sales_data_share;
GRANT USAGE ON SCHEMA sales_db.revenue TO SHARE sales_data_share;
GRANT SELECT ON TABLE sales_db.revenue.transactions TO SHARE sales_data_share;
```

### 4.3 Adding a Consumer Account
The provider must add the consumer's Snowflake account to allow access.

```sql
ALTER SHARE sales_data_share ADD ACCOUNT = 'consumer_account_id';
```

### 4.4 Viewing Shares
List all shares in the provider's account.

```sql
SHOW SHARES;
```

## 5. Accessing Shared Data (Consumer Side)
### 5.1 Viewing Incoming Shares
A consumer can check available shared data.

```sql
SHOW IMPORTED DATABASES;
```

### 5.2 Creating a Database from a Share
To use shared data, the consumer creates a database from the share.

```sql
CREATE DATABASE sales_db_shared FROM SHARE provider_account.sales_data_share;
```

### 5.3 Querying the Shared Data
```sql
SELECT * FROM sales_db_shared.revenue.transactions;
```
The consumer can read but cannot modify or copy the data.

## 6. Revoking & Dropping Shares
### 6.1 Removing a Consumer
To revoke access, the provider removes the consumer’s account.

```sql
ALTER SHARE sales_data_share REMOVE ACCOUNT = 'consumer_account_id';
```

### 6.2 Dropping a Share
If a share is no longer needed, it can be deleted.

```sql
DROP SHARE sales_data_share;
```

## 7. Use Cases of Secure Data Sharing
- **Data Monetization** – Sell live data to external customers.
- **Cross-Department Sharing** – Share data within a company without duplication.
- **Collaboration with Partners** – Provide suppliers or vendors with real-time insights.
- **Regulatory Compliance** – Share data securely for audits or government reporting.

## 8. Key Benefits of Snowflake Shares
- 🚀 **Performance** – Consumers query real-time data without delays.
- 🔒 **Security** – Providers control access, ensuring no unauthorized copies.
- 💰 **Cost Savings** – No need to move or duplicate large datasets.

## 9. Summary
- **Shares enable live, read-only data access without duplication.**
- **Providers create shares and grant access to consumers.**
- **Consumers accept shares and query data in real time.**
- **No extra storage cost for consumers—only query execution is charged.**

This makes Secure Data Sharing in Snowflake a game-changer for modern data collaboration. 🚀