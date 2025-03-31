# Snowflake System Commands

| **Category**                | **Command**                                  | **Description** |
|-----------------------------|----------------------------------------------|----------------|
| **Session & Connection Management** | `USE DATABASE <db_name>;` | Switch to a specific database. |
|                             | `USE SCHEMA <schema_name>;` | Set the active schema. |
|                             | `USE WAREHOUSE <warehouse_name>;` | Set the active warehouse. |
|                             | `ALTER SESSION SET <param>=<value>;` | Modify session-level parameters. |
|                             | `SHOW CONNECTIONS;` | View active Snowflake connections. |
| **System Information & Monitoring** | `SHOW PARAMETERS;` | Display system parameters. |
|                             | `SHOW DATABASES;` | List all databases. |
|                             | `SHOW SCHEMAS;` | List schemas in the current database. |
|                             | `SHOW WAREHOUSES;` | List all warehouses. |
|                             | `SHOW USERS;` | List all users. |
|                             | `SHOW ROLES;` | List all roles. |
|                             | `SHOW GRANTS;` | View privileges assigned to roles/users. |
|                             | `SELECT SYSTEM$CURRENT_USER();` | Get the current user. |
|                             | `SELECT SYSTEM$CURRENT_ACCOUNT();` | Get the current account ID. |
| **Resource Monitoring & Usage** | `SHOW WAREHOUSE LOAD HISTORY;` | View warehouse load history. |
|                             | `SHOW QUERY_HISTORY;` | View historical query execution details. |
|                             | `SHOW TRANSACTIONS;` | List ongoing transactions. |
|                             | `SELECT SYSTEM$WHITESPACE_USAGE();` | Check available storage space. |
| **Warehouse & Compute Management** | `ALTER WAREHOUSE <warehouse_name> RESUME;` | Resume a suspended warehouse. |
|                             | `ALTER WAREHOUSE <warehouse_name> SUSPEND;` | Suspend a running warehouse. |
|                             | `ALTER WAREHOUSE <warehouse_name> SET <param>=<value>;` | Modify warehouse settings. |
| **Security & Role Management** | `GRANT <privileges> ON <object> TO <role>;` | Assign privileges. |
|                             | `REVOKE <privileges> ON <object> FROM <role>;` | Remove privileges. |
|                             | `SHOW GRANTS TO USER <username>;` | View user privileges. |
|                             | `SHOW GRANTS TO ROLE <role_name>;` | View role privileges. |
| **Miscellaneous System Functions** | `SELECT SYSTEM$WAIT(<seconds>);` | Pause execution for a given duration. |
|                             | `SELECT SYSTEM$ABORT_TRANSACTION('<txn_id>');` | Abort a specific transaction. |
|                             | `SELECT SYSTEM$GET_PRIVATELINK();` | Get PrivateLink connection details. |
|                             | `CALL SYSTEM$CLUSTERING_INFORMATION('<table_name>');` | Get clustering details of a table. |

