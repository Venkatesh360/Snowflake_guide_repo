# ACCOUNTADMIN Role in Snowflake

The **ACCOUNTADMIN** role in Snowflake is the highest-level administrative role with full control over all objects, users, and operations within an account. It inherits privileges from all other roles and is responsible for overall account management.

## Key Responsibilities of ACCOUNTADMIN

1. **Full Administrative Control**  
   - Can manage all objects, users, and roles across the entire Snowflake account.  
   - Has access to all databases, schemas, tables, warehouses, and integrations.

2. **Role and User Management**  
   - Can create, modify, and delete roles and users.  
   - Grants or revokes privileges on any object.

3. **Billing and Usage Monitoring**  
   - Manages account-level settings, including cost control and resource usage monitoring.  
   - Can access Snowflake's billing and credit usage reports.

4. **Security and Governance**  
   - Manages global security settings, including IP whitelisting and access policies.  
   - Can oversee encryption and data-sharing policies.

5. **Warehouse and Compute Resource Management**  
   - Can create, modify, and delete virtual warehouses.  
   - Controls compute resource allocation for cost efficiency.

## Granting ACCOUNTADMIN Role

To assign the **ACCOUNTADMIN** role to a user:
```sql
GRANT ROLE ACCOUNTADMIN TO USER <username>;
```
To revoke the role:
```sql
REVOKE ROLE ACCOUNTADMIN FROM USER <username>;
```

## Best Practices for ACCOUNTADMIN

- **Limit Usage:** Only assign the **ACCOUNTADMIN** role to a few trusted users to prevent unauthorized access.  
- **Use Lower-Level Roles:** Perform most administrative tasks using **SYSADMIN** or **SECURITYADMIN** roles to minimize risk.  
- **Monitor Activity:** Regularly audit **ACCOUNTADMIN** actions using Snowflake's query history and access logs.

## Hierarchy of ACCOUNTADMIN

```
ACCOUNTADMIN
  ├── SYSADMIN
  │     ├── Custom Roles
  ├── SECURITYADMIN
  │     ├── USERADMIN
  ├── PUBLIC (Default role for all users)
