
### Query size optimisation

- If a table is partitioned, e.g. by date, we need to ***filter by partitioned fields*** to meaningfully reduce query size.

---

### JOINs
- Use **`FULL OUTER JOIN`** if you want to keep data from both tables, and use `COALESCE` to pick values from one table if it doesn't exist in another.

---

### MAX()

#### Text:

```sql
WITH CTE AS (
	SELECT 'NEW' AS col1
	UNION ALL
	SELECT NULL AS col1
)

SELECT MAX(col1) FROM CTE; -- 'NEW'
```

#### Numbers:
```sql
WITH CTE AS (
	SELECT 1 AS col1
	UNION ALL
	SELECT NULL AS col1
)

SELECT MAX(col1) FROM CTE; -- 1
```