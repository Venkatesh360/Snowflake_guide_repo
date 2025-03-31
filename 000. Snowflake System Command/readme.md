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
| `SYSTEM$GET_BACKGROUND_TASK_HISTORY()` | Returns a history of background tasks executed in the system. | `SELECT SYSTEM$GET_BACKGROUND_TASK_HISTORY();` |
| `SYSTEM$LIST_DATABASES()` | Lists all databases in the current account. | `SELECT SYSTEM$LIST_DATABASES();` |
| `SYSTEM$LIST_SCHEMAS('<database_name>')` | Lists schemas within a specified database. | `SELECT SYSTEM$LIST_SCHEMAS('my_database');` |
| `SYSTEM$LIST_TABLES('<schema_name>')` | Lists tables in a specified schema. | `SELECT SYSTEM$LIST_TABLES('my_schema');` |
| `SYSTEM$LIST_VIEWS('<schema_name>')` | Lists views in a specified schema. | `SELECT SYSTEM$LIST_VIEWS('my_schema');` |
| `SYSTEM$GET_QUERY_STATUS('<query_id>')` | Returns the status of a running query. | `SELECT SYSTEM$GET_QUERY_STATUS('12345-67890-abcdef');` |
| `SYSTEM$CANCEL_QUERY('<query_id>')` | Cancels an active query. | `SELECT SYSTEM$CANCEL_QUERY('12345-67890-abcdef');` |
| `SYSTEM$GET_SESSION_HISTORY()` | Retrieves session activity history. | `SELECT SYSTEM$GET_SESSION_HISTORY();` |
| `SYSTEM$GET_OBJECT_DEPENDENCIES('<object_name>')` | Returns a list of objects that depend on the specified object. | `SELECT SYSTEM$GET_OBJECT_DEPENDENCIES('my_table');` |
| `SYSTEM$GET_STORAGE_USAGE('<table_name>')` | Retrieves storage usage details for a table. | `SELECT SYSTEM$GET_STORAGE_USAGE('my_table');` |
| `SYSTEM$GET_QUERY_HISTORY('<time_range>')` | Returns query execution history within a given timeframe. | `SELECT SYSTEM$GET_QUERY_HISTORY('LAST_24_HOURS');` |
| `SYSTEM$GET_PIPE_STATUS('<pipe_name>')` | Retrieves the current status of a Snowpipe. | `SELECT SYSTEM$GET_PIPE_STATUS('my_pipe');` |
| `SYSTEM$GET_STREAM_STATUS('<stream_name>')` | Returns the status of a stream. | `SELECT SYSTEM$GET_STREAM_STATUS('my_stream');` |
| `SYSTEM$GET_STAGE_USAGE('<stage_name>')` | Returns storage usage details for a named stage. | `SELECT SYSTEM$GET_STAGE_USAGE('my_stage');` |
| `SYSTEM$VALIDATE_POLICY('<policy_name>')` | Validates the configuration of a security policy. | `SELECT SYSTEM$VALIDATE_POLICY('my_policy');` |
| `SYSTEM$GENERATE_API_INTEGRATION_SECRET()` | Generates a secret for an API integration. | `SELECT SYSTEM$GENERATE_API_INTEGRATION_SECRET();` |
| `SYSTEM$GET_RESOURCE_MONITOR_STATUS('<monitor_name>')` | Retrieves the current status of a resource monitor. | `SELECT SYSTEM$GET_RESOURCE_MONITOR_STATUS('monitor1');` |
| `SYSTEM$GET_DATABASE_VERSION('<database_name>')` | Returns the version information of a specified database. | `SELECT SYSTEM$GET_DATABASE_VERSION('my_database');` |
| `SYSTEM$GET_LOCK_STATUS('<object_name>')` | Retrieves lock details for a database object. | `SELECT SYSTEM$GET_LOCK_STATUS('my_table');` |

