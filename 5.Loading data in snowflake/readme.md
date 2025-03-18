
# Loading Data in Snowflake

## Stages in Snowflake

Stages in Snowflake are storage locations used for loading and unloading data. They act as an intermediate layer between the source data and Snowflake tables.

### Types of Stages

1. **Internal Stages**
   - Managed by Snowflake.
   - Stores data within Snowflake before loading it into tables.
   - Types:
     - **User Stage:** Automatically created for each user.
     - **Table Stage:** Automatically created for each table.
     - **Named Stage:** Manually created for organized storage.

2. **External Stages**
   - Reside in cloud storage services like AWS S3, Azure Blob Storage, or Google Cloud Storage.
   - Require integration with Snowflake using storage credentials.
   
### Managing External Stages

To create and manage external stages, use the following SQL commands:

#### Creating an External Stage
```sql
CREATE OR REPLACE DATABASE MANAGE_DB;
CREATE OR REPLACE SCHEMA external_stages;

CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    URL='s3://bucketsnowflakes3'
    CREDENTIALS=(AWS_KEY_ID='ABCD_DUMMY_ID' AWS_SECRET_KEY='1234abcd_key');
```

#### Describing an External Stage
```sql
DESC STAGE MANAGE_DB.external_stages.aws_stage;
```

#### Altering an External Stage
```sql
ALTER STAGE aws_stage
    SET CREDENTIALS=(AWS_KEY_ID='XYZ_DUMMY_ID' AWS_SECRET_KEY='987xyz');
```

#### Creating a Publicly Accessible External Stage
```sql
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    URL='s3://bucketsnowflakes3';
```

#### Listing Files in a Stage
```sql
LIST @MANAGE_DB.external_stages.aws_stage;
```
