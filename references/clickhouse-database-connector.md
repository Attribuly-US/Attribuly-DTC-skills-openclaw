---
name: clickhouse-database-connector
version: 1.1.0
description: Connect to a customer's read-only ClickHouse database. Works with restricted accounts that only have SELECT privilege on specific views or tables.
---
# Skill: ClickHouse Database Connector for DTC Ecommerce

## 🎯 Purpose

Enable OpenClaw to read directly from the customer's ClickHouse or ClickHouse-compatible (ByteHouse, Tencent CKafka, Alibaba ApsaraDB for ClickHouse) analytics database. ClickHouse is typically used for high-volume event streams, ad spend imports, and pre-aggregated attribution data.

---

## 🔐 Environment Variables (Connection Setup)

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `CLICKHOUSE_HOST` | ✅ Yes | — | ClickHouse host (IP or domain) |
| `CLICKHOUSE_PORT` | Optional | `8123` | HTTP interface port (use `9000` for native TCP) |
| `CLICKHOUSE_USER` | ✅ Yes | `default` | ClickHouse username (read-only account) |
| `CLICKHOUSE_PASSWORD` | ✅ Yes | — | ClickHouse password (store as secret, never log) |
| `CLICKHOUSE_DATABASE` | ✅ Yes | `default` | Target database/schema name |
| `CLICKHOUSE_ALLOWED_TABLES` | ✅ Yes | — | Comma-separated list of table/view names this account can access. Example: `events,ad_spend_daily,sessions_agg` |
| `CLICKHOUSE_PROTOCOL` | Optional | `http` | `http` or `https` |
| `CLICKHOUSE_SECURE` | Optional | `false` | Enable TLS (`true` / `false`) |
| `CLICKHOUSE_TIMEOUT` | Optional | `30` | Query timeout in seconds |

**Access Model:** The database account is read-only and scoped to the tables/views listed in `CLICKHOUSE_ALLOWED_TABLES`. `system.tables` and `system.columns` require admin/read privilege and are typically **not accessible** for restricted accounts — use `DESCRIBE TABLE` on each declared table instead.

**Security Note:** Never echo or expose `CLICKHOUSE_PASSWORD` in any output or log. All queries MUST use parameterized placeholders — never interpolate user-provided strings directly into query text.

---

## 🔌 Step 1: Establish Connection & Validate

```sql
-- Connectivity ping
SELECT 1 AS ping;

-- Confirm correct database
SELECT currentDatabase() AS current_db;

-- Check ClickHouse version (does not require elevated privilege)
SELECT version() AS ch_version;
```

If the connection fails, report the failing variable name (never the value) and guide the user to check firewall rules (ClickHouse typically requires port 8123 to be open) and credentials.

**Important:** Also check that `CLICKHOUSE_ALLOWED_TABLES` is set. If it is empty or unset, halt and ask: _"Please set CLICKHOUSE_ALLOWED_TABLES to the comma-separated names of the tables or views your database account can access (e.g., `events,ad_spend_daily`)."_

---

## 🗺️ Step 2: Schema Discovery (Restricted-Access Mode)

**The account is read-only and scoped. `system.tables` and `system.columns` require elevated system privileges and will typically raise a permission error for restricted users. Do NOT use them.**

Instead, use the table list in `CLICKHOUSE_ALLOWED_TABLES` and call `DESCRIBE TABLE` on each name directly.

### 2.1 Verify Each Declared Table is Accessible

For each table name `T` in `CLICKHOUSE_ALLOWED_TABLES`, run:

```sql
-- Replace {T} with each table/view name from CLICKHOUSE_ALLOWED_TABLES
DESCRIBE TABLE {T};
```

This returns column names, types, default expressions, and comments without requiring any `system.*` privilege. If a table raises a permission error or does not exist, report it to the user and remove it from the working table list.

### 2.2 Inspect Partitioning / Sorting Key (Optional)

For MergeTree-family tables, knowing the sorting key is critical for query performance. The partition key is stored in `system.tables`, but restricted accounts typically cannot access `system.*`.

If `system.tables` is inaccessible, ask the user directly: _"What is the partition/date column for table `{T}`? (e.g., `toYYYYMM(event_time)` or just `event_time`)"_. This is needed for efficient date-range filtering.

---

## 📐 Step 3: Schema Reference

All table and column definitions are declared in a **dedicated schema file**:

**→ [references/clickhouse-schema.md](clickhouse-schema.md)**

OpenClaw MUST load and read `clickhouse-schema.md` before constructing any query.

- If `clickhouse-schema.md` contains no declared tables yet (still shows `???` placeholders), halt and say: _"The ClickHouse schema has not been configured yet. Please ask the skill owner to fill in `references/clickhouse-schema.md` with the real table and column definitions before proceeding."_
- Use only table names and column names found in that file. Never guess.
- Respect the `partition_key`, `sorting_key`, and `nullable` annotations when building WHERE clauses and aggregations.
- Use `enum_values` for any event-type or status column to know valid filter values.

---

## 📊 Step 4: Analysis Queries

> **This section must be filled in by the skill owner after Step 3 is complete.**
> All queries must use the exact real table and column names confirmed in Step 3.
> Do NOT write queries here using placeholder names like `{events_table}` or `{event_time_col}`.

*(To be filled in once Step 3 field mapping is confirmed)*

---

## 🔄 Step 5: Schema Mapping Confirmation Flow

When first accessing a customer's database:

1. **Read `CLICKHOUSE_ALLOWED_TABLES`** — this is the authoritative list of accessible tables/views.
2. **Load `clickhouse-schema.md`** — check if tables are already declared.
3. **If `clickhouse-schema.md` is complete**, use it directly and proceed to Step 4.
4. **If `clickhouse-schema.md` is not yet filled in**:
   a. Run `DESCRIBE TABLE {T}` for each table in `CLICKHOUSE_ALLOWED_TABLES`.
   b. Ask the user for the partition/date column for each table (cannot be read without `system.*` access).
   c. Present the column list to the user and ask them to confirm the role of each table and the purpose of key columns.
   d. Do not guess or infer. Ask the skill owner to record the confirmed mapping into `clickhouse-schema.md`.
5. **Do not proceed to query construction** until `clickhouse-schema.md` is filled in.

---

## ⚡ ClickHouse Performance Best Practices

1. **Always filter on partition key first**: Use `toYYYYMM()` or `toDate()` in WHERE clauses — this enables partition pruning and avoids full-table scans.
2. **Use `uniq()` not `COUNT(DISTINCT)`**: ClickHouse's `uniq()` is ~10x faster for cardinality estimation.
3. **Prefer `countIf()` and `sumIf()`** over sub-queries for conditional aggregation.
4. **Avoid `JOIN` on large event tables**: Prefer pre-aggregated tables when available.
5. **Use `LIMIT` on exploratory queries**: Always add `LIMIT 1000` when doing schema discovery.
6. **Slow query diagnosis**: If a query is unexpectedly slow, ask the user to check `system.query_log` on their end — restricted accounts cannot access it directly.

---

## ⚠️ Query Safety Rules

1. **Read-Only by account**: The database account only has `SELECT` privilege. Do not attempt mutations (`ALTER TABLE ... UPDATE/DELETE`), `DROP`, `TRUNCATE`, or `INSERT` — they will be rejected and must never be generated.
2. **Scope to allowed tables only**: Only query tables/views listed in `CLICKHOUSE_ALLOWED_TABLES`. Never query other tables, `system.*`, or `information_schema.*` beyond what is explicitly noted above.
3. **Mandatory date filter**: Every query against event/spend tables MUST include a date range filter on the partition key column.
4. **Max rows guard**: Add `LIMIT 1000000` to any query that might return unbounded rows.
5. **No credential exposure**: Never output `CLICKHOUSE_PASSWORD` or connection strings in any response.
6. **Validate date inputs**: Ensure all date parameters match `YYYY-MM-DD` format before building queries.

---

## 📤 Output Formatting

- Use markdown tables for all results.
- Currency columns: 2 decimal places with currency symbol.
- Rate/percentage columns: 1 decimal place with `%` suffix.
- Large numbers: use thousands separator (e.g., `1,234,567`).
- Always include a "Change vs. Prior Period" column with positive changes in green (⬆️) and negative in red (⬇️) indicators.
