# Solution

Solution for the assessment mini project from Data Flows Sdn. Bhd.

---

## Architecture

#### **1. Bronze layer**
Raw data with minimal changes, mostly column name standardisation.

- `article_master`: Mock - Article Master Table
- `site_master`: Mock - Site Master Table
- `sales`: Mock - Sales Table

#### **2. Silver layer**
Metadata/main aggregation layer

- `sales_agg`: Aggregation of item sales amount and sales quantity, by date and site
- 


|Table name|purpose|columns needed


---

## Execution

## Import data

1. Briefly understand the requirements, expected output and inspect the data. Understanding: classify item Pareto ranking based on sales quantity and sales amount by site and category. Requirements
	- Store type = Retail store
	- Sales Period from 01/01/2023 until 31/03/2023
	- Pareto ranking by both **sales quantity** and **sales amount**

2. Convert the raw XLSX files into CSV.


3. Import CSV files data into BigQuery with schema on 'Auto detect'. Standardise columns to `snake_case`.

	```sql
	SELECT * FROM `dataflows-assessment.raw.article-master`;
	SELECT * FROM `dataflows-assessment.raw.site-master`;
	SELECT * FROM `dataflows-assessment.raw.sales`;
	```

---

### Notes:

Converting .xlsx to .csv files:
```sh
# install xlsx2csv Python package
python3 -m venv myvenv
source myvenv/bin/activate
pip install xlsx2csv

# convert .xlsx to .csv files
xlsx2csv raw-excel/Mock\ -\ Article\ Master\ Table.xlsx raw-csv/article-master.csv
xlsx2csv raw-excel/Mock\ -\ Sales\ Table.xlsx raw-csv/sales.csv
xlsx2csv raw-excel/Mock\ -\ Site\ Master\ Table.xlsx raw-csv/site-master.csv
```
