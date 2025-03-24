# Cron in Snowflake Tasks

## 1️⃣ What is Cron in Snowflake?
Cron expressions in Snowflake allow you to schedule tasks at specific times or intervals. They follow the standard cron syntax and always use UTC time.

## 2️⃣ Cron Expression Format
```
┌───────── Minute (0-59)
│ ┌───────── Hour (0-23)
│ │ ┌───────── Day of Month (1-31)
│ │ │ ┌───────── Month (1-12)
│ │ │ │ ┌───────── Day of Week (0-6) [Sunday = 0]
│ │ │ │ │
│ │ │ │ │
* * * * * 
```
Use `*` to mean "every" (e.g., `* * * * *` runs every minute).

Snowflake always runs tasks in UTC (convert local time to UTC if needed).

## 3️⃣ Examples of Cron Scheduling in Snowflake

| Cron Expression                 | Schedule                                     |
|----------------------------------|----------------------------------------------|
| `'USING CRON * * * * * UTC'`    | Runs every minute                           |
| `'USING CRON 0 * * * * UTC'`    | Runs every hour (at minute 0)               |
| `'USING CRON 0 6 * * * UTC'`    | Runs every day at 6 AM UTC                  |
| `'USING CRON 0 0 1 * * UTC'`    | Runs on the first day of every month at midnight |
| `'USING CRON 0 12 * * 1 UTC'`   | Runs every Monday at 12 PM UTC              |
| `'USING CRON 30 23 * * 5 UTC'`  | Runs every Friday at 11:30 PM UTC           |

## 4️⃣ Using Cron in Snowflake Tasks
✅ **Example: Schedule a Task to Run Daily at 3 AM UTC**
```sql
CREATE TASK daily_sales_report
WAREHOUSE = my_warehouse
SCHEDULE = 'USING CRON 0 3 * * * UTC'
AS
INSERT INTO sales_summary 
SELECT region, SUM(amount) FROM sales GROUP BY region;
```
🔹 This runs every day at 3 AM UTC to update the `sales_summary` table.

## 5️⃣ Advanced Cron Examples
✅ **Run Every Monday, Wednesday, and Friday at 8 AM UTC**
```sql
SCHEDULE = 'USING CRON 0 8 * * 1,3,5 UTC'
```
💡 *Tip: Use `,` to specify multiple days.*

✅ **Run on the First and Last Day of Every Month at 6 PM UTC**
```sql
SCHEDULE = 'USING CRON 0 18 1,L * * UTC'
```
💡 *Tip: `L` stands for the last day of the month.*

✅ **Run Every 15 Minutes**
```sql
SCHEDULE = 'USING CRON */15 * * * * UTC'
```
💡 *Tip: Use `/` for intervals (e.g., every 15 minutes).*

## 6️⃣ Finding Current UTC Time for Scheduling
Since Snowflake uses UTC time, you may need to check your current local time and convert it.

✅ **Check Current UTC Time in Snowflake**
```sql
SELECT CURRENT_TIMESTAMP();
```
💡 *Convert your local time to UTC before scheduling tasks.*

## 7️⃣ Summary
✔ Cron schedules tasks at specific times or intervals.
✔ Snowflake tasks use UTC time (convert if needed).
✔ You can run tasks daily, hourly, or on specific days using cron syntax.
✔ Use `SHOW TASKS` to check schedules.
