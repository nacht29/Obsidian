
Parent: [[Work/Cloud Space|Cloud Space]]


**Syntax (BigQuery)**

```sql
SUM(total_enrolment) OVER ( -- calculate cumulative headcount
	PARTITION BY division, year_group_id
	ORDER BY term_order
	ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) AS total_headcount,
```
