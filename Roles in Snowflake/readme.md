
# Roles in Snowflake

Roles in Snowflake define user permissions and control access to objects such as databases, warehouses, and schemas. Snowflake follows a **role-based access control (RBAC)** model, where users are assigned roles, and roles are granted specific privileges.

## Default System Roles
Snowflake provides several predefined roles with varying levels of access:

- **ACCOUNTADMIN**  
  - The highest-level role with full control over the Snowflake account.  
  - Can manage users, roles, warehouses, security policies, and billing.  
  - Recommended for a limited number of users due to its extensive privileges.

- **SYSADMIN**  
  - Has full control over databases, schemas, and warehouses but does not manage users or security.  
  - Can create and modify objects within the account.  
  - Typically assigned to data engineers or database administrators.

- **SECURITYADMIN**  
  - Manages user accounts, role grants, and security-related configurations.  
  - Can create and manage roles but does not have full control over databases.  
  - Essential for managing role-based security.

- **USERADMIN**  
  - Responsible for creating, modifying, and deleting users and roles.  
  - Does not have access to databases or warehouses.  
  - Typically assigned to administrators managing user accounts.

- **PUBLIC**  
  - The default role assigned to all users.  
  - Has minimal privileges, typically used for viewing public objects.

## Custom Roles
Organizations can define custom roles to fit their specific needs:

- **Data Engineer Role**  
  - Can create and manage schemas, tables, and warehouses.  
  - Requires `CREATE DATABASE`, `CREATE SCHEMA`, and warehouse-related privileges.

- **Data Analyst Role**  
  - Read-only access to specific schemas and tables.  
  - Requires `SELECT` privileges but not modification rights.

- **Application Role**  
  - Assigned to external applications accessing Snowflake via APIs.  
  - Requires specific privileges on required objects without exposing unnecessary data.

## Role Hierarchy & Best Practices
![Role Hierarchy](https://docs.snowflake.com/en/_images/system-role-hierarchy.png)

- Snowflake allows roles to be **granted to other roles**, forming a hierarchy.
- The **ACCOUNTADMIN** role has access to all other roles.
- **Best Practices:**
  - Follow the **principle of least privilege**—grant only the necessary permissions.
  - Use **separate roles for different responsibilities** (e.g., `DATA_INGESTION`, `REPORTING`).
  - Regularly **review and audit role assignments** to ensure compliance.
