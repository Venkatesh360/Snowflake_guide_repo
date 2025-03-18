# Handling Rejected Data in Snowflake

This document outlines how to manage rejected data when loading it into **Snowflake**, covering options like size limits, returning failed rows, truncating columns, and force-loading data.

## ⚡ Key Options:
- **Size Limit (`SIZE_LIMIT`)**: Ensures data does not exceed file and row size constraints.
- **Returning Failed Rows (`RETURN_FAILED_ONLY`)**: Retrieves only the rows that failed.
- **Truncate Columns (`TRUNCATECOLUMNS`)**: Truncates oversized values instead of rejecting rows.
- **Force Load (`FORCE`)**: Forces reloading of previously loaded data.

---

## 1️⃣ Size Limit (`SIZE_LIMIT`)
- Snowflake enforces limits on **file and row sizes**.
- Rows exceeding **16 MB** or files exceeding the configured limit will be **rejected**.
- To control file size, configure **staging parameters**.

---

## 2️⃣ Returning Failed Rows Only (`RETURN_FAILED_ONLY`)
- This option returns only the **failed rows** from a `COPY INTO` operation.
- Useful for **debugging** data load issues.

### ✅ Example:
```sql
COPY INTO my_table 
FROM @my_stage
ON_ERROR = 'CONTINUE'
RETURN_FAILED_ONLY = TRUE;
```
- With `RETURN_FAILED_ONLY = TRUE`, only **failed rows** appear in the result.

---

## 3️⃣ Truncate Columns (`TRUNCATECOLUMNS`)
- Allows Snowflake to **truncate values** that exceed column limits instead of **rejecting the row**.
- Useful when dealing with **unpredictable data lengths**.

### ✅ Example:
```sql
COPY INTO my_table
FROM @my_stage
FILE_FORMAT = (TYPE = 'CSV' TRUNCATECOLUMNS = TRUE);
```
- Without this, rows with **oversized values** would be **rejected**.

---

## 4️⃣ Force Load (`FORCE`)
- Normally, Snowflake **skips duplicate loads** using metadata tracking.
- `FORCE = TRUE` forces data to **reload**, even if it was already loaded.

### ✅ Example:
```sql
COPY INTO my_table
FROM @my_stage
FILE_FORMAT = (TYPE = 'CSV')
FORCE = TRUE;
```
- Useful when dealing with **partially loaded or modified data files**.

---

## 📌 Summary
These settings help control **error handling** and **data integrity** in Snowflake while ensuring smooth ingestion.

💡 **Reference**: [Snowflake Documentation](https://docs.snowflake.com)
