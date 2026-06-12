# Execution Plan

Parent: [[Work/Data Flows Sdn Bhd/Interview|Interview]]

## 1. Bronze layer

Purpose: Store the raw imported data from the provided source files with minimal transformation.

### 1.1 `sales`

Raw sales transaction table.

Columns:
- `bill_date`: Sales transaction date. Used to filter the sales period from 2023-01-01 to 2023-03-31.
- `site_id`: Site/store where the sale happened. Used to join with `site_master`.
- `item_id`: Item/product sold. Used to join with `article_master`.
- `quantity`: Quantity sold for the item. Used for Pareto classification by sales quantity.
- `sales_amount`: Sales amount for the item. Used for Pareto classification by sales amount.

### 1.2 `article_master`

Raw item master table.

Columns:
- `item_id`: Unique item/product identifier. Used to join with `sales`.
- `merchandise_cat`: Merchandise category code. Used as one of the Pareto grouping dimensions.
- `merchandise_cat_desc`: Merchandise category description. Used for reporting or validation.

### 1.3 `site_master`

Raw site master table.

Columns:
- `site_id`: Unique site/store identifier. Used to join with `sales`.
- `store_type`: Type of store/site. Used to filter only Retail store records.

## 2. Silver layer

Purpose: Clean, join, filter, aggregate, and calculate Pareto contribution metrics.

### 2.1 `sales_agg`

Filtered and enriched sales table after joining sales with article and site master data.

Columns:
- `bill_date`: Sales transaction date. Used to confirm the required sales period.
- `site_id`: Site/store where the sale happened.
- `store_type`: Store type from site master. Only Retail store records are kept.
- `item_id`: Item/product sold.
- `merchandise_cat`: Merchandise category from article master.
- `merchandise_cat_desc`: Merchandise category description from article master.
- `quantity`: Sales quantity from the sales table.
- `sales_amount`: Sales amount from the sales table.

Filters applied:
- `store_type = 'Retail store'`
- `bill_date >= '2023-01-01'`
- `bill_date <= '2023-03-31'`

### 2.2 `item_sales_agg`

Aggregated item-level sales table.

Grain:
- One row per `site_id`, `merchandise_cat`, and `item_id`.

Columns:
- `site_id`: Site/store where the item was sold.
- `merchandise_cat`: Merchandise category of the item.
- `item_id`: Item/product identifier.
- `total_qty`: Total sales quantity for the item within the site and merchandise category.
- `total_sales_amount`: Total sales amount for the item within the site and merchandise category.

Purpose:
- Converts transaction-level sales records into item-level totals.
- Provides the correct grain for Pareto calculation.

### 2.3 `item_pareto_metrics`

Pareto calculation table with contribution and cumulative percentage metrics.

Grain:
- One row per `site_id`, `merchandise_cat`, and `item_id`.

Columns:
- `site_id`: Site/store where the item was sold.
- `merchandise_cat`: Merchandise category of the item.
- `item_id`: Item/product identifier.
- `total_qty`: Total quantity sold for the item.
- `total_sales_amount`: Total sales amount for the item.
- `group_total_qty`: Total quantity sold for all items within the same site and merchandise category.
- `group_total_sales_amount`: Total sales amount for all items within the same site and merchandise category.
- `qty_contribution_pct`: Item quantity contribution percentage against `group_total_qty`.
- `qty_cum_pct_before`: Cumulative quantity percentage before adding the current item.
- `qty_cum_pct_after`: Cumulative quantity percentage after adding the current item.
- `sales_contribution_pct`: Item sales amount contribution percentage against `group_total_sales_amount`.
- `sales_cum_pct_before`: Cumulative sales amount percentage before adding the current item.
- `sales_cum_pct_after`: Cumulative sales amount percentage after adding the current item.

Purpose:
- Calculates each item's contribution within its site and merchandise category.
- Calculates cumulative percentages separately for quantity and sales amount.
- Quantity Pareto is sorted by `total_qty` descending.
- Sales amount Pareto is sorted by `total_sales_amount` descending.

## 3. Gold layer

Purpose: Produce the final report-ready Pareto classification output.

### 3.1 `pareto_item_classification`

Final Pareto classification table.

Grain:
- One row per `site_id`, `merchandise_cat`, and `item_id`.

Columns:
- `site_id`: Site/store where the item was sold.
- `merchandise_cat`: Merchandise category of the item.
- `item_id`: Item/product identifier.
- `total_qty`: Total quantity sold for the item.
- `total_sales_amount`: Total sales amount for the item.
- `qty_cum_pct_before`: Cumulative quantity percentage before adding the current item.
- `qty_cum_pct_after`: Cumulative quantity percentage after adding the current item.
- `pareto_class_by_qty`: Pareto class based on quantity contribution.
- `sales_cum_pct_before`: Cumulative sales amount percentage before adding the current item.
- `sales_cum_pct_after`: Cumulative sales amount percentage after adding the current item.
- `pareto_class_by_sales_amount`: Pareto class based on sales amount contribution.

Pareto classification logic:
- If cumulative percentage before current item is below 50%, classify as `A`.
- If cumulative percentage before current item is below 80%, classify as `B`.
- Otherwise, classify as `C`.

Final output requirement:
- Each item must have two Pareto classifications:
  - Pareto class by sales quantity
  - Pareto class by sales amount

Overall data flow:
1. Load raw files into Bronze tables.
2. Join sales with article master and site master.
3. Filter to Retail store and the required sales period.
4. Aggregate sales by site, merchandise category, and item.
5. Calculate contribution and cumulative percentages.
6. Assign Pareto A, B, or C classification.
7. Output the final report-ready Gold table.