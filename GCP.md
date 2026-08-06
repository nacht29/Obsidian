
## External table

- Allows user to modify BigQuery data from Google Sheets
- **Note:** This is different from **Connected Sheets**, where users **view BigQuery data in Google Sheets** but **cannot edit them**.
#### Query to create connected sheets:
```sql
CREATE EXTERNAL TABLE IF NOT EXISTS `bcg-bigquery-silver.r2_data.ext_income_matrix`
(
    ArticleSettlementCode STRING,
    ArticleSettlement STRING,
    IncomeA STRING,
    IncomeB STRING,
    ExtRefCat STRING,
    AAG STRING
)
OPTIONS (
    format = 'GOOGLE_SHEETS',
    uris = ['https://docs.google.com/spreadsheets/d/1C2hTCpL4_O1kgAc-SsjTcRc-ic2l4U17-b_K3WsWZFY/edit?usp=sharing'],
    sheet_range = 'Sheet1!A:F',
    skip_leading_rows = 1
);
```

---

## INFORMATION SCHEMA

### See which job modified a table:

This will also return the query used in the job.

```sql
SELECT
	job_id,
	user_email,
	query,
	creation_time,
	destination_table.dataset_id,
	destination_table.table_id
FROM `bcg-bigquery-silver.region-asia-southeast1.INFORMATION_SCHEMA.JOBS_BY_PROJECT`
WHERE destination_table.table_id = 'warehouse_receipt'
	AND destination_table.dataset_id = 'r3_data'
	AND creation_time > TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY)
ORDER BY creation_time DESC;
```

---
### Get all columns in a table:
```sql
SELECT 
	column_name, 
	data_type, 
	is_nullable
FROM 
	`bcg-bigquery-silver.r3_data.INFORMATION_SCHEMA.COLUMNS`
WHERE 
	table_name = 'warehouse_receipt'
ORDER BY 
	ordinal_position;
```
