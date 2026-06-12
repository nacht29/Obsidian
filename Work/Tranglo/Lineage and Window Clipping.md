

Parent: [[Work/Tranglo|Tranglo]]

## Problem
The ETL uses `dbo.Lineage` as a watermark to decide the next load window.

When latest lineage records are deleted, the stored procedure does not know those windows were already processed. It falls back to the latest remaining successful `DataUntil`.

Example:
- Deleted latest lineage: `2025-02-27 05:00 → 2025-02-27 10:00`
- Remaining max `DataUntil`: `2025-02-27 05:00`
- Next run starts again from `2025-02-27 05:00`

## Window Clipping Issue
Even if we pass a later date like `2026-06-01`, the procedure applies the 5-hour cap:

IF requested end date is more than 5 hours after previous DataUntil,
then actual DataEndDate becomes previous DataUntil + 5 hours.

So instead of loading until `2026-06-01`, it only loads:

`2025-02-27 05:00 → 2025-02-27 10:00`

## False Positive Fix
Deleting one lineage row and passing the deleted lineage's end date can make the load succeed, but this only proves that one clipped window was replayed.

It does not prove:
- the full intended restore range was loaded
- the requested latest date was respected
- the missing data was fully recovered
- business dates were labelled correctly

## Main Risk
If downstream logic hard-codes or uses the passed/latest date as the business date, old records can be loaded from an older window but incorrectly labelled as current/latest-date data.

Correct separation:
- Source transaction date = actual business date
- Lineage DataSince/DataUntil = ETL extraction window
- Load date = when ETL ran
- Requested DataEndDate = operator input

## Proper Solution
Do not use manual lineage deletion as the restore control mechanism.

Implement explicit load modes:

- Incremental mode:
  - uses latest successful lineage
  - applies 5-hour clipping

- Backfill / restore mode:
  - uses explicit start and end date
  - optionally bypasses 5-hour clipping
  - logs clear lineage
  - requires validation / safety flag

## Key Understanding
Lineage is the ETL memory/watermark.

Deleting lineage rolls the watermark backward, so old windows become eligible for loading again.

The 5-hour window cap is useful for normal incremental loads, but dangerous for restore/backfill if there is no explicit override.

## Knowledge Gained
- `MAX(DataUntil)` controls the next load start point.
- Deleting latest lineage does not reset everything; it falls back to the latest remaining successful lineage.
- A successful lineage status does not prove the intended restore range was fully processed.
- Restore/backfill logic should be explicit, not simulated by deleting lineage.
- Business dates must come from source data, not lineage window dates or operator input dates.