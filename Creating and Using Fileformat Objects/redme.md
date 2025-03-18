
# Creating and Using File Format Objects
```sql
CREATE OR REPLACE SCHEMA MANAGE_DB.file_formats;
CREATE OR REPLACE FILE FORMAT MANAGE_DB.file_formats.my_file_format;
DESC FILE FORMAT MANAGE_DB.file_formats.my_file_format;
```

## Using a File Format Object in COPY Command
```sql
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    FILE_FORMAT= (FORMAT_NAME=MANAGE_DB.file_formats.my_file_format)
    FILES = ('OrderDetails_error.csv')
    ON_ERROR = 'SKIP_FILE_3';
```

For detailed options, refer to the [Snowflake Documentation](https://docs.snowflake.com/).
