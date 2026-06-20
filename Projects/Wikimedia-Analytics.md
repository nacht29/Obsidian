Parent: [[Projects]]
## Data Sources

### 1. Wikimedia EventStreams — RecentChange

- **Link:** [https://stream.wikimedia.org/v2/stream/recentchange](https://stream.wikimedia.org/v2/stream/recentchange)
    
- **Docs:** [https://wikitech.wikimedia.org/wiki/Event_Platform/EventStreams_HTTP_Service](https://wikitech.wikimedia.org/wiki/Event_Platform/EventStreams_HTTP_Service)
    
- **Purpose:** Ingest near real-time Wikimedia edit/change events.
    
- **Use case:** Track edits, page creations, logs, categorisations, bots, users, namespaces, revisions, and change activity over time.
    
- **Ingestion method:** Python SSE consumer → Kafka raw topic → PostgreSQL raw table.
    

---

### 2. Wikimedia Pageviews / Reads

- **Dump link:** [https://dumps.wikimedia.org/other/pageviews/](https://dumps.wikimedia.org/other/pageviews/)
    
- **Docs:** [https://dumps.wikimedia.org/other/pageviews/readme.html](https://dumps.wikimedia.org/other/pageviews/readme.html)
    
- **Alternative API docs:** [https://doc.wikimedia.org/generated-data-platform/aqs/analytics-api/](https://doc.wikimedia.org/generated-data-platform/aqs/analytics-api/)
    
- **Purpose:** Capture article/project read activity.
    
- **Use case:** Analyse page traffic, read volume, reader trends, and compare reading activity against editing activity.
    
- **Ingestion method:** Batch load from dumps or API → PostgreSQL raw table.
    

---

### 3. Wikimedia Clickstream

- **Dump link:** [https://dumps.wikimedia.org/other/clickstream/](https://dumps.wikimedia.org/other/clickstream/)
    
- **Docs:** [https://dumps.wikimedia.org/other/clickstream/readme.html](https://dumps.wikimedia.org/other/clickstream/readme.html)
    
- **Purpose:** Capture aggregate reader navigation paths between pages.
    
- **Use case:** Analyse how users move between articles, common referer/resource paths, and content discovery patterns.
    
- **Ingestion method:** Batch load monthly dump files → PostgreSQL raw table.
    

---

## Expected Final Product

A mini Wikimedia analytics platform that ingests, stores, models, and analyses public Wikimedia activity data.

## Project Architecture

```text
Wikimedia EventStreams SSE
    → Python producer
    → Kafka raw topic
    → PostgreSQL raw layer
    → curated warehouse tables
    → analytics queries / dashboards

Wikimedia Pageviews Dumps/API
    → batch ingestion script
    → PostgreSQL raw layer
    → curated warehouse tables
    → analytics queries / dashboards

Wikimedia Clickstream Dumps
    → batch ingestion script
    → PostgreSQL raw layer
    → curated warehouse tables
    → analytics queries / dashboards
```

## Storage & Modelling

### Raw Layer

- Stores full source payloads with minimal transformation.
    
- Keeps raw JSON/event/file records for replay, debugging, and future remodelling.
    
- Example table:
    
    - `eventstream.recentchange_raw`
        

### Curated / Warehouse Layer

- Extracts useful fields from raw data into queryable tables.
    
- Models data by source/domain.
    
- Example schemas/tables:
    
    - `recentchange.content_changes`
        
    - `recentchange.logs`
        
    - `recentchange.categorisations`
        
    - `pageviews.pageview_daily`
        
    - `clickstream.navigation_paths`
        

## Orchestration

- **Current stage:** no Airflow or Spark needed.
    
- Use simple Python scripts and SQL transformations first.
    
- Add Airflow later only when batch ingestion and transformations become repeatable scheduled jobs.
    
- Add Spark later only if the data volume or transformation complexity justifies distributed processing.
    

## Current Focus

```text
1. Stabilise EventStreams SSE ingestion
2. Write full raw events into Kafka
3. Load Kafka events into PostgreSQL raw layer
4. Handle ingestion failures and logging
5. Then ingest Pageviews and Clickstream data
6. Then start raw → curated data modelling
```

## Project Goal

Build an end-to-end data engineering project showing:

- real-time ingestion with Kafka
    
- batch ingestion from public dumps/APIs
    
- raw-to-curated warehouse modelling
    
- reliable failure handling
    
- analytical tables for Wikimedia editing, reading, and navigation behaviour