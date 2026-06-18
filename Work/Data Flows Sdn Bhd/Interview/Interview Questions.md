# Interview Questions

Parent: [[Work/Data Flows Sdn Bhd/Interview|Interview]]


## Round 1
1. Mostly past experience questions
2. OTLP vs OLAP and how do you decide which one to use?
	- OLTP: Row by row read and write, good for recording fast moving transactional data. Use when the main purpose is to capture transactions like logs, bank records
	- OLAP: Column by column read and write. Use when need to store, query and analyse and large amounts of  historical data. The data will ideally be structured and cleaned then stored, allowing users to query on specific fields.
## Round 2
1. Questions on resume:
	- General work experience:
		- Eileen - scripting, recursions, optimisation, reconciliation logic
		- Tranglo - Develop, refactor ETL jobs, communicate with stakeholders for ambiguous calculations, metadata tables, and fields | Debug window clipping logic
		- Cloud Space - POC project, ingest from API, handle pagination (detect pagination params), standardise timestamp format, BG modelling, medallion layers, compile metadata in silver layer to use for later aggregations.
2.  Questions about assessment:
	1. Why do we use Pareto class instead of numerical values?
		- Assumption is that the company have specific workflows, metrics or calculation for different alphabetical Pareto class. It is also easier to read for non-technical people.
	2. Why by site and merchandise category?
		- By site so that we can tailor supply for demands at each site. Category because each category might contain items of different nature. For example Cat 1 is daily items like painkillers and vitamins, so sales will naturally be higher. Cat 2 might be items less in demand or more niche, like over the counter drugs. It would be unfair or inaccurate to calculate and represent Pareto classes if we mix the categories.
