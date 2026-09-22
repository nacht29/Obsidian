## What it is

- Tracks "last point already processed" per source, so incremental runs only pull deltas instead of re-scanning everything.
- Lives in `bcg-bigquery-silver.monitoring.load_control` — **pre-existing table, we didn't create it.** Belongs to another team (same pattern their `Merge Update R2 GL` script uses — that's where the pattern was copied from).
- One row per source:

| process_name | last_processed_recordstamp |
|---|---|
| dynamod_AcCARPOSSalesM_big | TIMESTAMP |
| dynamod_AcCARPOSSalesM_caring | TIMESTAMP |

- **Two separate watermarks (BIG / CARING) — not one universal value for the table.**

---

## Gotcha: source_timestamp is per-ROW, not a stream clock

- `datastream_metadata.source_timestamp` = epoch **milliseconds**. (Got bit once using `TIMESTAMP_MICROS` — showed 1970 dates. Always `TIMESTAMP_MILLIS`.)
- Set only when *that specific row* changes at the source. Doesn't tick just because the stream is running — an untouched row keeps its old timestamp forever.
- Per Google docs: [Events and streams](https://docs.cloud.google.com/datastream/docs/events-and-streams) — "timestamp (UTC) when the record changed on the source". [BigQuery destination](https://docs.cloud.google.com/datastream/docs/destination-bigquery) — SOURCE_TIMESTAMP is INTEGER, epoch ms.

---

## The flow

1. Read both watermarks from `load_control`. **If either is missing, script auto-seeds both to `CURRENT_TIMESTAMP() - 7 days` and keeps running** (no hard stop — check this hasn't drifted from the actual script if revisiting).
2. `CREATE TEMP TABLE batch` — filter each source: `source_timestamp > watermark - 6h` (`fallback_hours` var). Buffer for out-of-order events, free to keep wide since MERGE is idempotent.
3. From the batch (not the source again) — derive `batch_rows`, exact `DATE(DocumentDate)` list, new watermark = `MAX(recordstamp)` **from the batch itself**, not `CURRENT_TIMESTAMP()`.
4. Guard: RAISE if any NULL `DocumentDate`, RAISE if batch spans >4000 partitions.
5. `MERGE` into target — `ON` clause pinned to the batch's exact date list, prunes the target-side scan:
```sql
ON target.DBSource = source.DBSource
    AND target.DocumentNo = source.DocumentNo
    AND DATE(target.DocumentDate) BETWEEN min_doc_date AND max_doc_date
    AND DATE(target.DocumentDate) IN UNNEST(batch_partitions)
```
6. Only after MERGE commits: advance watermark in `load_control`, per source that actually had rows.

---

## Why source_timestamp, not LastSyncDate

- `LastSyncDate` = business column (SAP sync completion). NULL for pending syncs, doesn't move on every touch.
- ANDing both compares two unrelated clocks, keeps only the intersection — silently drops unsynced rows. Real bug from an earlier version, fixed. (not in current script comments, just remember this)

---

## Cost reality check

- Source (`dbo_AcCARPOSSalesM`, both BIG/CARING) is only `CLUSTER BY DocumentNo` in BQ console — no partitioning, no date clustering.
- First run (wide, 7-day bootstrap window): 339 GB.
- Caught-up run (narrow window, hours not days): 1.26 GB, 310,006 rows.
- Cost tracks batch window width, not a fixed floor. Stay caught up — a missed run widens the window and cost creeps back toward hundreds of GB.

---

## Files

- `Sales-Recon/src/dynamod_AcCARPOSSalesM.sql` — the pipeline
- `Sales-Recon/src/bootstrap_load_control.sql` — seed script (header comment claims it's required and RAISEs if skipped — check against #1 above, may be stale)
