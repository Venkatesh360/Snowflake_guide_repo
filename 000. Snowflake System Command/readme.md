# Snowflake SYSTEM$ Functions

| **Function** | **Description** | **Usage Example** |
|-------------|---------------|-------------------|
| `SYSTEM$CURRENT_USER()` | Returns the current logged-in user. | `SELECT SYSTEM$CURRENT_USER();` |
| `SYSTEM$CURRENT_ACCOUNT()` | Returns the current account identifier. | `SELECT SYSTEM$CURRENT_ACCOUNT();` |
| `SYSTEM$CURRENT_REGION()` | Returns the cloud region of the current account. | `SELECT SYSTEM$CURRENT_REGION();` |
| `SYSTEM$WHITESPACE_USAGE()` | Returns available and used storage space. | `SELECT SYSTEM$WHITESPACE_USAGE();` |
| `SYSTEM$WAIT(<seconds>)` | Pauses execution for a given number of seconds. | `SELECT SYSTEM$WAIT(10);` |
| `SYSTEM$ABORT_TRANSACTION('<txn_id>')` | Aborts a specific transaction. | `SELECT SYSTEM$ABORT_TRANSACTION('1234567890');` |
| `SYSTEM$GET_PRIVATELINK()` | Retrieves PrivateLink connection details. | `SELECT SYSTEM$GET_PRIVATELINK();` |
| `SYSTEM$GET_SNOWFLAKE_PLATFORM_INFO()` | Returns platform information such as version details. | `SELECT SYSTEM$GET_SNOWFLAKE_PLATFORM_INFO();` |
| `SYSTEM$CLUSTERING_INFORMATION('<table_name>')` | Returns clustering information for a table. | `CALL SYSTEM$CLUSTERING_INFORMATION('my_table');` |
| `SYSTEM$ESTIMATE_TABLE_COMPRESSED_SIZE('<table_name>')` | Estimates compressed storage size for a table. | `SELECT SYSTEM$ESTIMATE_TABLE_COMPRESSED_SIZE('my_table');` |
| `SYSTEM$GET_TAG_ON_CURRENT_OBJECT('<tag_name>')` | Retrieves the value of a tag on the current object. | `SELECT SYSTEM$GET_TAG_ON_CURRENT_OBJECT('sensitive_data');` |
| `SYSTEM$GET_REFERENCE('<object_name>')` | Returns a reference link to a Snowflake object. | `SELECT SYSTEM$GET_REFERENCE('my_table');` |
| `SYSTEM$TYPEOF(<expression>)` | Returns the data type of an expression. | `SELECT SYSTEM$TYPEOF(123.45);` |
| `SYSTEM$EXTRACT_JSON_FIELD('<json_string>', '<field_path>')` | Extracts a field from a JSON string. | `SELECT SYSTEM$EXTRACT_JSON_FIELD('{"name": "John"}', 'name');` |
| `SYSTEM$GET_LOGICAL_REPLICATION_STATUS()` | Returns the status of logical replication. | `SELECT SYSTEM$GET_LOGICAL_REPLICATION_STATUS();` |
| `SYSTEM$GET_TRANSACTION_INFO('<txn_id>')` | Returns metadata about a transaction. | `SELECT SYSTEM$GET_TRANSACTION_INFO('1234567890');` |
| `SYSTEM$GENERATE_SCIM_ACCESS_TOKEN()` | Generates an SCIM access token for user provisioning. | `SELECT SYSTEM$GENERATE_SCIM_ACCESS_TOKEN();` |
| `SYSTEM$GET_PASSWORD_POLICY('<policy_name>')` | Retrieves details of a password policy. | `SELECT SYSTEM$GET_PASSWORD_POLICY('strong_policy');` |
| `SYSTEM$GET_SESSION_PARAMETERS()` | Retrieves current session parameters. | `SELECT SYSTEM$GET_SESSION_PARAMETERS();` |
| `SYSTEM$IS_ROLE_IN_SESSION('<role_name>')` | Checks if a role is active in the session. | `SELECT SYSTEM$IS_ROLE_IN_SESSION('ACCOUNTADMIN');` |

