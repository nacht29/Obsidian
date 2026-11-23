# Enterprise data pipeline — study notes

> Session summary for a data engineering intern. Covers ingestion through serving, with follow-up clarifications on Kafka internals, failure recovery, and distributed hashing.

---

## Table of contents

1. [Terminologies](#1-terminologies)
2. [Full pipeline architecture](#2-full-pipeline-architecture)
   - [Layer 0 — Data sources](#layer-0--data-sources)
   - [Layer 1 — Ingestion (Kafka deep dive)](#layer-1--ingestion)
   - [Layer 2 — Processing](#layer-2--processing)
   - [Layer 3 — Storage](#layer-3--storage)
   - [Layer 4 — Orchestration](#layer-4--orchestration)
   - [Layer 5 — Serving](#layer-5--serving)
   - [Cross-cutting — Failure recovery and the DLQ](#cross-cutting--failure-recovery-and-the-dlq)
   - [Cross-cutting — Scaling and sharding](#cross-cutting--scaling-and-sharding)
   - [Cross-cutting — Observability, quality, governance](#cross-cutting--observability-quality-and-governance)
3. [Minimal mental model and footnotes](#3-minimal-mental-model-and-footnotes)

---

## 1. Terminologies

### Core Kafka terms

| Term | Plain definition |
|---|---|
| **Broker** | A single Kafka server. A Kafka cluster is made up of multiple brokers. Each broker stores some partitions and handles read/write requests for them. |
| **Topic** | A named, logical channel for a category of events (e.g. `user-events`, `order-placed`). Think of it as a folder; messages are the files inside. |
| **Partition** | A topic is split into N partitions. Each partition is an ordered, append-only log stored on a broker. Partitions hold *different slices* of the topic's data — they are NOT copies of each other. Together, all partitions contain the full dataset. |
| **Replica** | A copy of a single partition stored on a different broker. Replicas exist purely for fault tolerance. If a broker dies, the replica on another broker becomes the new leader. Replicas do NOT add more data — they are identical copies of their partition. |
| **Leader / Follower** | For each partition, one broker is the leader (handles all reads and writes). Others are followers (sync the data from the leader, ready to take over). |
| **Producer** | Any application that *writes* messages to a Kafka topic. A producer decides which partition to send a message to, usually by hashing a key (e.g. `user_id`). |
| **Consumer** | Any application that *reads* messages from a Kafka topic. Consumers track their own position (offset) and can replay data by rewinding their offset. |
| **Consumer group** | A set of consumers that divide up the work of reading a topic. Each partition is assigned to exactly one consumer in the group at a time — this is how Kafka parallelises reads. |
| **Offset** | A monotonically increasing integer assigned to each message within a partition. `offset=0` is the first message ever written to that partition. Consumers commit their current offset to track progress; on restart, they resume from the last committed offset. |
| **Connector / Kafka Connect** | A framework for running source connectors (pulling data into Kafka from external systems) and sink connectors (pushing data out of Kafka to external systems) without writing custom producer/consumer code. |

### Distributed systems terms

| Term | Plain definition |
|---|---|
| **Shard** | A horizontal slice of a dataset stored on a separate server (node). Sharding is how you scale a database beyond what a single machine can hold. |
| **Shard key** | The field used to decide which shard a record belongs to (e.g. `user_id`, `post_id`). Choosing the right shard key is critical — a bad choice causes hot shards. |
| **Hash** | A deterministic function that takes any input and produces a fixed-size number (e.g. `hash("alice") = 4829302`). The same input always produces the same output. Used in sharding to decide which shard holds a given record. |
| **Hash space** | The full range of values a hash function can produce (e.g. 0 to 2³² for a 32-bit hash). In consistent hashing, this range is imagined as a circle. |
| **Node** | A single server/instance in a distributed cluster. In a database cluster, each node holds some shards. |
| **Instance** | Often used interchangeably with node — one running copy of a service or database. "Scaling out" means adding more instances. |
| **Replication factor** | How many copies of each partition (or shard) to keep across nodes. A replication factor of 3 means 1 leader + 2 follower replicas. |
| **Hot shard** | A shard that receives disproportionately more traffic than others (e.g. all new users land on the same shard). Causes performance degradation on that node while others sit idle. |

### Pipeline-level terms

| Term | Plain definition |
|---|---|
| **DAG** | Directed Acyclic Graph. The data structure orchestrators use to represent pipeline tasks and their dependencies. Directed = edges have a direction (A must run before B). Acyclic = no loops (the pipeline has a clear end). |
| **DLQ** | Dead Letter Queue. A separate queue where messages are sent after all retry attempts fail. Prevents data loss; allows human inspection and replay after fixing the root cause. |
| **CDC** | Change Data Capture. A technique (used by tools like Debezium) that reads a database's transaction log and streams every insert/update/delete as an event into Kafka. Allows real-time sync without polling. |
| **SLA** | Service Level Agreement. In pipelines, usually means a time guarantee — "the daily revenue report must be ready by 08:00 UTC." Orchestrators track whether SLAs are met and alert if they're breached. |
| **Idempotent** | An operation that produces the same result no matter how many times it's applied. Idempotent processing is essential for retries — if a message is processed twice due to a retry, you don't double-count. |
| **Backpressure** | A mechanism where a downstream stage signals to an upstream stage to slow down because it's overwhelmed. Prevents cascading failures across the pipeline. |
| **Schema registry** | A central service that stores Avro/Protobuf message schemas. Producers register a schema and get a schema ID; consumers look up the schema by ID to deserialise correctly. Enforces backward/forward compatibility to prevent breaking changes. |
| **Data lineage** | A record of where a piece of data came from and what transformations it has passed through. Used for debugging ("why is this revenue number wrong?") and compliance ("where is this PII field used?"). |

---

## 2. Full pipeline architecture

The pipeline flows through six layers, with cross-cutting concerns (observability, failure recovery, scaling) running alongside every layer.

![[Pasted image 20260629001705.png]]

---

### Layer 0 — Data sources

Data enters your pipeline from many origins simultaneously:

- **Databases** (PostgreSQL, MySQL) — read via CDC (Debezium reads the transaction log) or batch export
- **REST / GraphQL APIs** — polled on a schedule or via webhooks
- **HTTP Event Streams (SSE)** — long-lived HTTP connections that push events in real-time
- **Files** (CSV, Parquet, JSON) — landed in S3 or an SFTP server, picked up by batch jobs
- **Clickstream** — browser/app events sent directly to an event collector
- **IoT sensors** — high-volume, low-latency device telemetry

---

### Layer 1 — Ingestion

#### What Kafka actually is

Kafka is a **distributed, durable, ordered message log**. It decouples producers (writers) from consumers (readers) — they never talk to each other directly. Kafka holds data for a configurable retention window (e.g. 7 days), allowing consumers to replay from any point.

#### Producers and consumers

- A **producer** is any application that writes messages into a Kafka topic. The producer decides the partition using a message key: `partition = hash(key) % num_partitions`. Because the same key always maps to the same partition, all events for a given `user_id` always arrive in the same partition — preserving order for that user.
- A **consumer** reads messages from one or more partitions. It tracks its position using an **offset** (a sequential integer per partition). On crash and restart, it resumes from the last committed offset — enabling safe replay.

#### Partitions: slices, not copies

A topic with 3 partitions and 1 million events distributes those events across partitions (roughly 333k each). **Partitions contain different data.** The complete topic = all partitions combined.

Replication is separate: each partition is independently replicated across N broker nodes. A replica is a copy of *that partition*, not of other partitions.

![[Pasted image 20260629001714.png]]

```
Topic: "user-events"  (3 partitions, replication factor = 3)

Partition 0 ──► [msg for user 101] [msg for user 101] ...
                   replicated to broker-2 and broker-3

Partition 1 ──► [msg for user 202] [msg for user 202] ...
                   replicated to broker-1 and broker-3

Partition 2 ──► [msg for user 303] [msg for user 303] ...
                   replicated to broker-1 and broker-2

Consumer group reads: Consumer-0 ← P0, Consumer-1 ← P1, Consumer-2 ← P2
```

#### When Kafka's upstream source fails (HTTP EventStream outage)

> This is a different failure mode from a write failure (handled by DLQ). Here, the connector to the upstream source goes down — events emitted by the source during the outage are never received by Kafka at all.

Kafka can only protect data it has already received. Recovery depends on what the upstream source supports:

![[Pasted image 20260629001827.png]]

**Strategy 1 — Source supports cursor/Last-Event-ID (zero data loss)**

The SSE protocol has a built-in `Last-Event-ID` header. A well-implemented connector persists the last received event ID to durable storage (not in memory — memory is lost on crash). On reconnect, it sends `Last-Event-ID` in the HTTP header, and the server replays all missed events from that point.

```
Source ──(normal)──► Connector ──► Kafka
         t=0                        t=10 (connector crashes)

Connector restores, sends:
  GET /stream
  Last-Event-ID: <last-seen-id>

Source replays events from that ID ──► Connector ──► Kafka
```

**Strategy 2 — Source has a REST API with timestamp filtering (recoverable)**

No SSE cursor, but the source exposes a REST endpoint like `GET /events?since=<timestamp>`. On recovery, a backfill job queries the gap window (`t_last_seen` to `now`), publishes those events into Kafka, then resumes the live stream.

**Strategy 3 — Source is ephemeral, no replay (data loss accepted by design)**

Some sources (live sensor ticks, certain third-party streams) have no history. The architecture response is to: accept the gap, log the gap window in metadata so downstream consumers know to treat that period as unreliable, and set SLA targets around maximum tolerable downtime.

**Key rule:** make connectors stateful. The last-seen cursor/timestamp/sequence ID must be persisted to durable storage (e.g. a database row, an S3 file, or Kafka Connect's internal offset topic) — never held only in memory.

---

### Layer 2 — Processing

This layer transforms raw ingested data into clean, enriched, and aggregated datasets.

- **Batch processing** (Apache Spark, dbt): runs on a schedule, processes large historical windows. Latency is minutes to hours.
- **Stream processing** (Apache Flink, Kafka Streams): processes each event as it arrives. Latency is milliseconds to seconds.
- **Hybrid** (Apache Beam): write once, run as batch or stream.

Key transforms: deduplication, schema normalisation, joining streams (e.g. enriching an order event with product metadata), windowed aggregations (e.g. "revenue in the last 5 minutes"), and filtering/masking PII.

---

### Layer 3 — Storage

| Tier | What it is | Tools |
|---|---|---|
| **Data lake** | Raw, unprocessed files in cheap object storage. Schema-on-read. | S3, GCS, ADLS |
| **Data warehouse** | Structured, query-optimised, schema-on-write. Fast SQL analytics. | Snowflake, BigQuery, Redshift |
| **Lakehouse** | Combines lake economics with warehouse performance. Adds ACID transactions, time travel, and schema enforcement on top of object storage. | Apache Iceberg, Delta Lake, Apache Hudi |

---

### Layer 4 — Orchestration

Orchestrators solve a critical problem: multi-step pipelines have *dependencies*. Task B cannot run until Task A succeeds. Task C and D can run in parallel if they don't depend on each other. And if Task E fails, Tasks F and G should be blocked.

An orchestrator models this as a **DAG**:

![[Pasted image 20260629002046.png]]

When `transform_events` fails, the orchestrator:
1. Fires an alert (PagerDuty, Slack)
2. Retries automatically (up to N times)
3. Marks the SLA as missed if time threshold is exceeded
4. Blocks all downstream children (e.g. `join_all`, `load_warehouse`) from running

**Tools:** Apache Airflow (most widely used), Dagster (asset-based, better for data engineering), Prefect (simpler Python-native API).

**Critical distinction from cron jobs:** cron fires and forgets. An orchestrator tracks state, handles dependencies, retries failures, emits metrics, and gives you a visual history of every run.

---

### Layer 5 — Serving

How the processed data reaches end consumers:

- **BI tools** (Tableau, Looker, Metabase) query the warehouse directly via SQL
- **Feature store** (Feast, Tecton): pre-computed ML features served at low latency (<10ms) to model inference endpoints
- **Reverse ETL** (Census, Hightouch): syncs warehouse data back to operational tools (Salesforce CRM, HubSpot) — the data flows back "upstream"
- **Data APIs**: REST or GraphQL endpoints backed by a query engine (Trino/Presto) for ad-hoc queries
- **Semantic layer** (dbt Semantic Layer): defines metric calculations once (`revenue = sum(amount) where status='paid'`), so "revenue" means the same thing everywhere

---

### Cross-cutting — Failure recovery and the DLQ

Every stage of the pipeline can fail. The rule is: **never silently drop a message.**

![[Pasted image 20260629002201.png]]

#### Retry with exponential backoff + jitter

When a message fails to process, it goes to a retry queue. Wait time grows exponentially between attempts:

```
Attempt 1 → wait 2s
Attempt 2 → wait 8s    (2 × 2²)
Attempt 3 → wait 30s   (2 × 2³ + jitter)
...
Attempt N → DLQ
```

**Jitter** (±20% random variance) is essential. Without it, if 10,000 consumers all fail simultaneously, they retry in perfect synchrony — creating a thundering herd that hammers the downstream system in waves.

#### Circuit breaker

If error rate exceeds a threshold (e.g. >50% of messages failing), the circuit breaker *opens* — stopping all retries for a cool-down period. This lets a struggling downstream service recover instead of being continuously hammered.

```
Closed (normal) ──(error rate > threshold)──► Open (stop retrying)
      ▲                                              │
      └────────────(cool-down expires)───────────────┘
                  Half-open (test one request)
```

#### Dead Letter Queue (DLQ)

After all retries are exhausted, the message is parked in the DLQ. It is never deleted. An alert fires, an engineer inspects the message, identifies the root cause (schema change? upstream bug? invalid data?), fixes it, and replays the message back into the main queue.

---

### Cross-cutting — Scaling and sharding

#### Horizontal vs vertical scaling

- **Scale up (vertical):** bigger machine (more RAM, faster CPU). Limited ceiling, expensive.
- **Scale out (horizontal):** more machines. Preferred in enterprise. Requires sharding.

![[Pasted image 20260629002335.png]]
#### Regular hash sharding

`shard = hash(shard_key) % N`

Simple and effective, but has two problems:

1. **Hot shards:** if your shard key is a timestamp, all new data goes to the same shard. Use a high-cardinality key like `user_id` instead.
2. **Reshuffling on resize:** add a shard (N becomes N+1) and nearly every key maps to a new shard. All data must be moved. This is catastrophic for a live system.

#### Consistent hashing — the fix for reshuffling

Instead of a modulo table, imagine a ring spanning the full hash space (0 to 2³²):

![[Pasted image 20260629002226.png]]

*Source: [ByteByteGo — Consistent Hashing](https://www.youtube.com/watch?v=_PtlQY59_8c)*

**How it works:**

1. Both *nodes* and *keys* are hashed to positions on the ring.
2. Each key is owned by the **first node clockwise** from its position.
3. **Add a node:** it only takes keys from its counter-clockwise neighbour. All other assignments are unchanged.
4. **Remove a node:** its keys transfer to its clockwise neighbour only.

**Result:** with N nodes and K keys, adding one node remaps only `K/N` keys — not all K. Cassandra, Redis Cluster, and DynamoDB all use consistent hashing for this reason.

**Virtual nodes:** each physical server is placed at multiple positions on the ring (e.g. 150 virtual nodes per server). This prevents uneven load if physical nodes cluster on one arc of the ring.

---

### Cross-cutting — Observability, quality, and governance

#### Observability (the "three pillars")

| Pillar | What it tracks | Tools |
|---|---|---|
| **Metrics** | Throughput, Kafka consumer lag, error rate, latency | Prometheus + Grafana, Datadog |
| **Logs** | Structured JSON logs from every pipeline component | Elasticsearch, Loki |
| **Traces** | End-to-end request tracing across services | OpenTelemetry, Jaeger |

**Kafka consumer lag** is the most important ingestion health metric — it measures how far behind a consumer group is from the latest message in a partition. High lag = the consumer can't keep up with the producer.

#### Data quality

- **Expectation tests** (Great Expectations, dbt tests): assertions run on every dataset — "this column must not be null," "these IDs must be unique," "revenue must be > 0."
- **Quarantine:** bad rows are never silently dropped. They go to a quarantine table for inspection.
- **Data contracts:** a formal agreement between the team producing data and the teams consuming it, specifying schema, SLA, and quality guarantees.

#### Schema registry

Prevents silent breaking changes. Producers register schemas (Avro/Protobuf) and receive a schema ID. Consumers look up the schema by ID to deserialise. The registry enforces compatibility rules — you cannot add a required field without a default value, because that would break existing consumers.

#### Data lineage and catalogue

- **Column-level lineage:** tracks the exact origin of every field through every transformation.
- **Impact analysis:** "if I rename column `rev` to `revenue` in this source table, what downstream dashboards break?"
- **Business glossary:** "revenue" is defined once in the catalogue; every dashboard and report refers to that single definition.
- **GDPR compliance:** lineage lets you find every place a user's PII appears so you can delete it on a right-to-erasure request.

---

## 3. Minimal mental model and footnotes

### The six-word summary

> Ingest → Process → Store → Orchestrate → Serve → Observe

### Eight rules to hold in your head

1. **Data is never dropped — it's parked.** Failed messages → DLQ. Bad rows → quarantine table. Schema-breaking payloads → rejected at the registry.

2. **Kafka partitions = different slices. Replicas = same slice, different broker.** Partitions divide the data. Replicas duplicate one partition for safety.

3. **Consumers own their offset.** Kafka doesn't push data — consumers pull, at their own pace. A crashed consumer resumes from its last committed offset.

4. **Upstream source failures ≠ write failures.** If the connector to your source dies, Kafka never received those events. Recovery depends on whether the source supports cursor-based replay. Make connectors stateful — persist the cursor to disk, not memory.

5. **Idempotent operations make retries safe.** Processing the same message twice must produce the same result. Design for this from day one.

6. **Jitter prevents thundering herds.** Add ±20% random variance to retry wait times so thousands of consumers don't retry simultaneously.

7. **Hash sharding splits data. Consistent hashing minimises reshuffling.** Regular `hash % N` remaps everything when N changes. Consistent hashing only remaps `1/N` of keys.

8. **Orchestrators ≠ cron jobs.** Orchestrators track state, handle dependencies, retry failures, and measure SLAs. Never use cron for multi-step pipelines.

---

### Quick-reference: partition vs replica

| | Partitions | Replicas |
|---|---|---|
| **Purpose** | Parallelism and scale | Fault tolerance |
| **Data** | Different slices of the topic | Identical copy of one partition |
| **Location** | Spread across brokers | Same partition copied to multiple brokers |
| **Consumer** | Each consumed by one consumer in a group | Followers do not serve reads (in Kafka) |

---

### Quick-reference: failure modes

| Failure | Where it's handled | Recovery |
|---|---|---|
| Message fails to process | Retry queue + DLQ | Fix root cause → replay from DLQ |
| Upstream source is down (HTTP stream) | Connector cursor / backfill job | Resume with `Last-Event-ID` or REST backfill |
| Kafka broker dies | Partition replica on another broker | Automatic leader election |
| Consumer crashes | Offset checkpoint | Resume from last committed offset |
| Downstream service overloaded | Circuit breaker | Open circuit → cool-down → retry |
| DAG task fails | Orchestrator | Alert → retry → block children |

---

### Tools cheat sheet

| Layer | Common tools |
|---|---|
| Ingestion | Apache Kafka, AWS Kinesis, Google Pub/Sub, Debezium (CDC) |
| Processing | Apache Spark, Apache Flink, dbt, Apache Beam |
| Storage (lake) | S3, GCS, Azure Data Lake Storage |
| Storage (warehouse) | Snowflake, BigQuery, Redshift |
| Storage (lakehouse) | Apache Iceberg, Delta Lake, Apache Hudi |
| Orchestration | Apache Airflow, Dagster, Prefect |
| Observability | Prometheus, Grafana, Datadog, OpenTelemetry |
| Data quality | Great Expectations, dbt tests |
| Schema | Confluent Schema Registry |
| Lineage / catalogue | OpenLineage, DataHub, Amundsen, Alation |
| Serving | Tableau, Looker, Feast, Trino, Census |
| Infrastructure | Terraform, Kubernetes, Helm, ArgoCD |

---

*End of notes. Last updated: June 2026.*
