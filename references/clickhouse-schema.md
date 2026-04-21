---
name: clickhouse-schema
version: 1.0.0
description: Declares the exact tables and fields accessible via the ClickHouse read-only account. OpenClaw reads this file to construct all SQL queries. Must be filled in by the skill owner before any query is executed.
---

# ClickHouse Schema Declaration

> **How to fill in this file:**
> 1. For each table in `CLICKHOUSE_ALLOWED_TABLES`, run `DESCRIBE TABLE <table_name>;` in your ClickHouse client.
> 2. Copy the output columns into the corresponding table block below.
> 3. Mark the role of each table (`events` / `ad_spend` / `sessions` / `attribution` / `other`).
> 4. For each column, fill in: exact column name, ClickHouse data type, and a brief description.
> 5. Mark the partition/date column as `partition_key: true` — OpenClaw MUST filter on this column for every query to enable partition pruning.
> 6. Mark the primary key / sorting key columns as `sorting_key: true`.
> 7. Mark columns that are nullable as `nullable: true` (ClickHouse uses `Nullable(T)` types).
> 8. For event-type columns, list the known event values in `enum_values` — OpenClaw will use these for conditional aggregation.

---

## Table Definitions

<!--
  Add one block per accessible table/view.
  Copy the template below and repeat for each table.
-->

### Template (copy and fill in for each table)

```yaml
- table: <exact_table_or_view_name>
  role: <events|ad_spend|sessions|attribution|other>
  description: <one line describing what this table contains>
  engine: <MergeTree|ReplacingMergeTree|AggregatingMergeTree|View|...>
  columns:
    - name: <column_name>
      type: <DateTime|Date|String|UInt64|Int64|Float64|Nullable(String)|LowCardinality(String)|...>
      description: <what this column represents>
      partition_key: true   # remove if not the partition/date filter column
      sorting_key: true     # remove if not part of ORDER BY / primary key
      nullable: true        # remove if NOT nullable
      enum_values:          # only for low-cardinality event/status columns
        - <value1>
        - <value2>
```

---

## Declared Tables

*(Fill in one block per table below. Remove this line when done.)*

---

## Query Constraints

> OpenClaw MUST follow these rules when building queries against this schema:

- Only use tables and columns declared above. Never guess or add columns not listed here.
- Always filter on the column marked `partition_key: true` in every query — this is required for ClickHouse partition pruning. Without it, queries will do a full table scan.
- Use `enum_values` to know the valid values for event type / status filtering. Never assume event names not listed here.
- Never access `system.*` tables — the account does not have permission.
- For columns of type `Nullable(T)`, wrap in `ifNull(col, default)` or check `IS NOT NULL` before aggregating.
- If the user asks for a metric that requires a column not present in this schema, respond: _"That field is not available in the current schema. Please check if it needs to be added to the allowed table list."_
