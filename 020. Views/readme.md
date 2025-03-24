## Views in Snowflake: Normal vs Secure Views

Snowflake provides two types of views:

1. **Normal Views (Standard Views)**
2. **Secure Views**

Both allow users to create virtual tables that represent the results of a SQL query without storing data separately. However, Secure Views offer additional security and access control.

### 1. What is a View in Snowflake?
A view is a saved SQL query that behaves like a table. When queried, the view dynamically retrieves data from the underlying tables.

#### ✅ Key Benefits of Views:
- Simplify complex queries.
- Restrict access to specific columns or rows.
- Improve data abstraction and security.

### 2. Normal Views (Standard Views)
A normal view (also called a non-secure view) is the default type of view in Snowflake.

#### 2.1 Creating a Normal View
```sql
CREATE VIEW customer_view AS
SELECT customer_id, first_name, last_name, email
FROM customers;
```

💡 **How it works:**
- The view retrieves data dynamically.
- Metadata is visible to all users with access.
- The view's definition (SQL query) is exposed when queried using `SHOW VIEWS` or `DESC VIEW`.

#### 2.2 Limitations of Normal Views
- **Lack of Security**: The SQL definition is visible to all users who have access to the view.
- **No Fine-Grained Control**: Cannot restrict access to sensitive columns.

### 3. Secure Views in Snowflake
A **Secure View** hides metadata and provides better security by preventing unauthorized access to the underlying SQL logic and metadata.

#### 3.1 Creating a Secure View
```sql
CREATE SECURE VIEW secure_customer_view AS
SELECT customer_id, first_name, email
FROM customers;
```

💡 **How it works:**
- The SQL query definition is **hidden** from users.
- The view **inherits** the permissions of the owner rather than the executing user.
- **Cannot be shared** across accounts using Snowflake’s Secure Data Sharing.

#### 3.2 Benefits of Secure Views
✅ **Metadata Protection**: Prevents unauthorized users from seeing column names, underlying tables, and SQL logic.
✅ **Better Access Control**: Useful when hiding sensitive data while allowing read access to non-sensitive columns.
✅ **Restrict Data Exposure**: Supports **Row-Level Security (RLS)** by applying `WHERE` conditions.

### 4. Key Differences: Normal vs Secure Views
| Feature | Normal View | Secure View |
|---------|------------|-------------|
| **Query Definition Visibility** | Visible to all users with access | Hidden from non-owners |
| **Security** | Low (metadata is exposed) | High (metadata is protected) |
| **Data Sharing** | ✅ Yes (can be shared with other Snowflake accounts) | ❌ No (cannot be shared across accounts) |
| **Performance** | ✅ Fast | ❌ Slightly slower due to security processing |
| **Use Case** | General query simplification | Sensitive data access control |

### 5. Example Use Cases
| Scenario | Recommended View Type |
|----------|----------------------|
| Hiding sensitive columns (e.g., salary, SSN) | Secure View |
| Providing aggregated results for reports | Normal View |
| Restricting row access based on user role | Secure View |
| Query abstraction for BI tools | Normal View |
| Preventing access to query logic | Secure View |

### 6. When to Use Secure Views?
- When you need **strict data security**.
- When hiding table structures or SQL queries from users.
- When using **Row-Level Security (RLS)** to limit access to specific data.
- When preventing **metadata exposure** in multi-tenant environments.

### 7. Example: Using Secure Views for Row-Level Security (RLS)
A Secure View can enforce **Row-Level Security** by filtering rows based on the current user.

```sql
CREATE SECURE VIEW sales_rep_orders AS
SELECT order_id, customer_id, amount
FROM orders
WHERE sales_rep = CURRENT_USER();
```

✅ **This ensures that each sales representative sees only their own orders.**

### 8. Summary
- **Normal Views** are great for general use but **expose metadata**.
- **Secure Views** provide better security by **hiding SQL logic** and **restricting access**.
- **Secure Views** are ideal for handling **sensitive data** like **PII (Personally Identifiable Information)**.
- **Secure Views cannot be shared** across Snowflake accounts, while **Normal Views can**.
