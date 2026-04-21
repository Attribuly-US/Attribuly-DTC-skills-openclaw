---
name: clickhouse-database-connector
version: 1.0.0
description: Connect to a customer's ClickHouse database, auto-discover analytics event tables and fields, and query attribution/funnel/ad-spend data for marketing analysis.
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
| `CLICKHOUSE_USER` | ✅ Yes | `default` | ClickHouse username |
| `CLICKHOUSE_PASSWORD` | ✅ Yes | — | ClickHouse password (store as secret, never log) |
| `CLICKHOUSE_DATABASE` | ✅ Yes | `default` | Target database/schema name |
| `CLICKHOUSE_PROTOCOL` | Optional | `http` | `http` or `https` |
| `CLICKHOUSE_SECURE` | Optional | `false` | Enable TLS (`true` / `false`) |
| `CLICKHOUSE_TIMEOUT` | Optional | `30` | Query timeout in seconds |

**Security Note:** Never echo or expose `CLICKHOUSE_PASSWORD` in any output or log. All queries MUST use parameterized placeholders — never interpolate user-provided strings directly into query text.

---

## 🔌 Step 1: Establish Connection & Validate

```sql
-- Connectivity ping
SELECT 1 AS ping;

-- Confirm correct database
SELECT currentDatabase() AS current_db;

-- Check ClickHouse version
SELECT version() AS ch_version;
```

If the connection fails, report the failing variable name (never the value) and guide the user to check firewall rules (ClickHouse typically requires port 8123 to be open) and credentials.

---

## 🗺️ Step 2: Schema Discovery

### 2.1 List All Tables with Row Counts and Sizes

```sql
SELECT
  database,
  name                                                          AS table_name,
  engine,
  formatReadableSize(total_bytes)                               AS size,
  total_rows,
  toDate(metadata_modification_time)                            AS last_modified
FROM system.tables
WHERE database = currentDatabase()
  AND engine NOT IN ('View', 'MaterializedView', 'Dictionary')
ORDER BY total_rows DESC;
```

### 2.2 Inspect All Columns in a Table

```sql
SELECT
  name                  AS column_name,
  type                  AS data_type,
  default_kind,
  default_expression,
  comment
FROM system.columns
WHERE database = currentDatabase()
  AND table = '{table_name}'
ORDER BY position;
```

### 2.3 Auto-Detect Analytics & Marketing Table Candidates

```sql
SELECT
  name         AS table_name,
  total_rows,
  engine
FROM system.tables
WHERE database = currentDatabase()
  AND (
    name LIKE '%event%'    OR name LIKE '%pageview%'  OR name LIKE '%page_view%' OR
    name LIKE '%session%'  OR name LIKE '%visit%'     OR
    name LIKE '%click%'    OR name LIKE '%impression%' OR
    name LIKE '%conversion%' OR name LIKE '%purchase%' OR name LIKE '%order%' OR
    name LIKE '%ad_spend%' OR name LIKE '%spend%'     OR name LIKE '%campaign%' OR
    name LIKE '%attribution%' OR name LIKE '%funnel%' OR
    name LIKE '%customer%' OR name LIKE '%user%'
  )
ORDER BY total_rows DESC;
```

### 2.4 Check Partitioning Keys (Important for Date Filtering Performance)

```sql
SELECT
  table,
  partition_key,
  sorting_key,
  primary_key
FROM system.tables
WHERE database = currentDatabase()
  AND name = '{table_name}';
```

**Always use the partition/sorting key in WHERE clauses for performance.**

---

## 📐 Step 3: Schema Mapping

### Events / Raw Analytics Table

| Canonical Field | Common ClickHouse Column Names |
|-----------------|---------------------------------|
| `event_id` | `event_id`, `id`, `uuid` |
| `event_type` | `event_type`, `event_name`, `action`, `type` |
| `user_id` | `user_id`, `visitor_id`, `anonymous_id`, `distinct_id` |
| `session_id` | `session_id`, `visit_id` |
| `event_time` | `event_time`, `timestamp`, `created_at`, `ts`, `event_at` |
| `page_url` | `page_url`, `url`, `page`, `current_url` |
| `referrer` | `referrer`, `referral_url`, `ref` |
| `utm_source` | `utm_source`, `source` |
| `utm_medium` | `utm_medium`, `medium` |
| `utm_campaign` | `utm_campaign`, `campaign` |
| `utm_content` | `utm_content`, `ad_content` |
| `utm_term` | `utm_term`, `keyword` |
| `device_type` | `device_type`, `device`, `platform` |
| `country` | `country`, `country_code`, `geo_country` |
| `city` | `city`, `geo_city` |
| `properties` | `properties`, `event_data`, `metadata` (JSON column) |

### Ad Spend / Marketing Cost Table

| Canonical Field | Common ClickHouse Column Names |
|-----------------|---------------------------------|
| `spend_date` | `date`, `report_date`, `day`, `spend_date` |
| `platform` | `platform`, `channel`, `source`, `ad_network` |
| `account_id` | `account_id`, `ad_account_id` |
| `campaign_id` | `campaign_id` |
| `campaign_name` | `campaign_name`, `campaign` |
| `ad_set_id` | `ad_set_id`, `adset_id`, `ad_group_id` |
| `ad_set_name` | `ad_set_name`, `adset_name`, `ad_group_name` |
| `ad_id` | `ad_id`, `creative_id` |
| `ad_name` | `ad_name`, `creative_name` |
| `spend` | `spend`, `cost`, `amount_spent` |
| `impressions` | `impressions`, `views`, `reach` |
| `clicks` | `clicks`, `link_clicks` |
| `platform_conversions` | `conversions`, `reported_purchases` |
| `platform_revenue` | `reported_revenue`, `conversion_value` |

### Sessions / Aggregated Visits Table

| Canonical Field | Common ClickHouse Column Names |
|-----------------|---------------------------------|
| `session_id` | `session_id`, `visit_id` |
| `user_id` | `user_id`, `visitor_id` |
| `session_start` | `session_start`, `started_at`, `first_event_time` |
| `session_end` | `session_end`, `ended_at`, `last_event_time` |
| `session_duration` | `duration`, `session_duration_seconds` |
| `utm_source` | `utm_source`, `source` |
| `utm_medium` | `utm_medium`, `medium` |
| `utm_campaign` | `utm_campaign`, `campaign_name` |
| `landing_page` | `landing_page`, `entry_page`, `first_url` |
| `page_views` | `page_views`, `pageview_count` |
| `is_conversion` | `is_conversion`, `converted`, `has_purchase` |
| `revenue` | `revenue`, `order_revenue`, `purchase_value` |

### Attribution / Conversion Table

| Canonical Field | Common ClickHouse Column Names |
|-----------------|---------------------------------|
| `conversion_id` | `conversion_id`, `order_id`, `purchase_id` |
| `user_id` | `user_id`, `customer_id` |
| `conversion_time` | `conversion_time`, `converted_at`, `purchase_time` |
| `revenue` | `revenue`, `order_value`, `gmv` |
| `channel` | `channel`, `attribution_channel`, `last_click_channel` |
| `campaign` | `campaign`, `attribution_campaign` |
| `model` | `model`, `attribution_model` |
| `attribution_credit` | `credit`, `attribution_weight`, `fractional_value` |
| `is_new_customer` | `is_new_customer`, `is_new`, `new_visitor_flag` |

---

## 📊 Step 4: Core Analysis Queries

### Q1: Daily Pageview & Session Funnel

```sql
SELECT
  toDate({event_time_col})                                       AS report_date,
  countIf({event_type_col} = 'pageview')                        AS pageviews,
  uniqIf({session_id_col}, {event_type_col} = 'pageview')       AS sessions,
  uniqIf({session_id_col}, {event_type_col} = 'product_view')   AS product_view_sessions,
  uniqIf({session_id_col}, {event_type_col} = 'add_to_cart')    AS add_to_cart_sessions,
  uniqIf({session_id_col}, {event_type_col} = 'checkout_start') AS checkout_sessions,
  uniqIf({session_id_col}, {event_type_col} = 'purchase')       AS purchase_sessions,
  round(
    uniqIf({session_id_col}, {event_type_col} = 'purchase')
    / nullIf(uniqIf({session_id_col}, {event_type_col} = 'pageview'), 0) * 100,
    2
  )                                                              AS overall_cvr_pct
FROM {events_table}
WHERE toDate({event_time_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY report_date
ORDER BY report_date ASC;
```

### Q2: UTM Channel Attribution (Sessions & Conversions)

```sql
SELECT
  ifNull({utm_source_col}, 'direct')                            AS utm_source,
  ifNull({utm_medium_col}, 'none')                              AS utm_medium,
  ifNull({utm_campaign_col}, 'none')                            AS utm_campaign,
  uniq({session_id_col})                                        AS sessions,
  uniqIf({session_id_col}, {event_type_col} = 'purchase')       AS converting_sessions,
  round(
    uniqIf({session_id_col}, {event_type_col} = 'purchase')
    / nullIf(uniq({session_id_col}), 0) * 100,
    2
  )                                                             AS cvr_pct,
  sumIf({revenue_col}, {event_type_col} = 'purchase')           AS total_revenue
FROM {events_table}
WHERE toDate({event_time_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY utm_source, utm_medium, utm_campaign
ORDER BY total_revenue DESC
LIMIT 50;
```

### Q3: Ad Spend vs. Attributed Revenue (ROAS Calculation)

```sql
SELECT
  s.platform                                                     AS platform,
  s.campaign_name                                                AS campaign_name,
  round(sum(s.spend), 2)                                        AS total_spend,
  round(sum(e.revenue), 2)                                      AS attributed_revenue,
  round(sum(e.revenue) / nullIf(sum(s.spend), 0), 2)           AS roas,
  round(sum(s.spend) / nullIf(sum(e.conversions), 0), 2)       AS cpa
FROM (
  SELECT
    platform,
    campaign_name,
    sum({spend_col})        AS spend,
    {spend_date_col}        AS report_date
  FROM {ad_spend_table}
  WHERE {spend_date_col} BETWEEN '{start_date}' AND '{end_date}'
  GROUP BY platform, campaign_name, report_date
) s
LEFT JOIN (
  SELECT
    ifNull({utm_campaign_col}, 'unknown')                       AS campaign_name,
    toDate({event_time_col})                                    AS report_date,
    sumIf({revenue_col}, {event_type_col} = 'purchase')        AS revenue,
    uniqIf({session_id_col}, {event_type_col} = 'purchase')    AS conversions
  FROM {events_table}
  WHERE toDate({event_time_col}) BETWEEN '{start_date}' AND '{end_date}'
  GROUP BY campaign_name, report_date
) e ON s.campaign_name = e.campaign_name AND s.report_date = e.report_date
GROUP BY platform, campaign_name
ORDER BY total_spend DESC;
```

### Q4: New vs. Returning User Sessions

```sql
WITH user_first_session AS (
  SELECT
    {user_id_col}           AS user_id,
    min(toDate({event_time_col})) AS first_seen_date
  FROM {events_table}
  GROUP BY {user_id_col}
)
SELECT
  toDate(e.{event_time_col})                                    AS report_date,
  countIf(e.{event_type_col} = 'purchase')                     AS total_purchases,
  countIf(
    e.{event_type_col} = 'purchase'
    AND toDate(e.{event_time_col}) = ufs.first_seen_date
  )                                                             AS new_user_purchases,
  countIf(
    e.{event_type_col} = 'purchase'
    AND toDate(e.{event_time_col}) > ufs.first_seen_date
  )                                                             AS returning_user_purchases,
  round(sumIf({revenue_col}, e.{event_type_col} = 'purchase' AND toDate(e.{event_time_col}) = ufs.first_seen_date), 2) AS new_user_revenue,
  round(sumIf({revenue_col}, e.{event_type_col} = 'purchase' AND toDate(e.{event_time_col}) > ufs.first_seen_date), 2) AS returning_user_revenue
FROM {events_table} e
JOIN user_first_session ufs ON e.{user_id_col} = ufs.user_id
WHERE toDate(e.{event_time_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY report_date
ORDER BY report_date ASC;
```

### Q5: Funnel Stage Drop-off by Channel

```sql
SELECT
  ifNull({utm_source_col}, 'direct')                                  AS utm_source,
  uniq({session_id_col})                                               AS sessions,
  uniqIf({session_id_col}, {event_type_col} = 'product_view')         AS to_product_view,
  uniqIf({session_id_col}, {event_type_col} = 'add_to_cart')          AS to_add_to_cart,
  uniqIf({session_id_col}, {event_type_col} = 'checkout_start')       AS to_checkout,
  uniqIf({session_id_col}, {event_type_col} = 'purchase')             AS to_purchase,
  round(uniqIf({session_id_col}, {event_type_col} = 'product_view')   / nullIf(uniq({session_id_col}), 0) * 100, 1) AS pv_rate,
  round(uniqIf({session_id_col}, {event_type_col} = 'add_to_cart')    / nullIf(uniqIf({session_id_col}, {event_type_col} = 'product_view'), 0) * 100, 1) AS atc_rate,
  round(uniqIf({session_id_col}, {event_type_col} = 'checkout_start') / nullIf(uniqIf({session_id_col}, {event_type_col} = 'add_to_cart'), 0) * 100, 1) AS checkout_rate,
  round(uniqIf({session_id_col}, {event_type_col} = 'purchase')       / nullIf(uniqIf({session_id_col}, {event_type_col} = 'checkout_start'), 0) * 100, 1) AS purchase_rate
FROM {events_table}
WHERE toDate({event_time_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY utm_source
ORDER BY sessions DESC
LIMIT 20;
```

### Q6: Platform Ad Spend WoW Comparison

```sql
SELECT
  platform,
  campaign_name,
  round(sumIf({spend_col}, toDate({spend_date_col}) BETWEEN '{prev_start}' AND '{prev_end}'), 2) AS prev_spend,
  round(sumIf({spend_col}, toDate({spend_date_col}) BETWEEN '{curr_start}' AND '{curr_end}'), 2) AS curr_spend,
  round(
    (sumIf({spend_col}, toDate({spend_date_col}) BETWEEN '{curr_start}' AND '{curr_end}')
    - sumIf({spend_col}, toDate({spend_date_col}) BETWEEN '{prev_start}' AND '{prev_end}'))
    / nullIf(sumIf({spend_col}, toDate({spend_date_col}) BETWEEN '{prev_start}' AND '{prev_end}'), 0) * 100,
    1
  ) AS spend_change_pct,
  round(sumIf({impressions_col}, toDate({spend_date_col}) BETWEEN '{curr_start}' AND '{curr_end}'), 0) AS curr_impressions,
  round(sumIf({clicks_col}, toDate({spend_date_col}) BETWEEN '{curr_start}' AND '{curr_end}'), 0)      AS curr_clicks
FROM {ad_spend_table}
WHERE toDate({spend_date_col}) BETWEEN '{prev_start}' AND '{curr_end}'
GROUP BY platform, campaign_name
ORDER BY curr_spend DESC
LIMIT 50;
```

### Q7: Landing Page Performance

```sql
SELECT
  {landing_page_col}                                           AS landing_page,
  uniq({session_id_col})                                       AS sessions,
  uniqIf({session_id_col}, {event_type_col} = 'add_to_cart')  AS atc_sessions,
  uniqIf({session_id_col}, {event_type_col} = 'purchase')     AS purchase_sessions,
  round(uniqIf({session_id_col}, {event_type_col} = 'purchase') / nullIf(uniq({session_id_col}), 0) * 100, 2) AS cvr_pct,
  round(sumIf({revenue_col}, {event_type_col} = 'purchase'), 2) AS revenue
FROM {events_table}
WHERE toDate({event_time_col}) BETWEEN '{start_date}' AND '{end_date}'
  AND {landing_page_col} IS NOT NULL
GROUP BY landing_page
ORDER BY sessions DESC
LIMIT 30;
```

---

## 🔄 Step 5: Schema Mapping Confirmation Flow

1. **Run Step 2 discovery queries** to list all tables and columns.
2. **Present the match table** to the customer:

```
Detected Tables:
✅ events_raw         → mapped as [events_table]       (1.2B rows)
✅ ad_spend_daily     → mapped as [ad_spend_table]     (450K rows)
✅ sessions_agg       → mapped as [sessions_table]     (80M rows)
❓ conversions_v2     → unclear, please confirm purpose
```

3. **Confirm partition key** for the primary events table — this is critical for query performance in ClickHouse.
4. **Persist mapping** in conversation context:

```json
{
  "events_table": "events_raw",
  "ad_spend_table": "ad_spend_daily",
  "sessions_table": "sessions_agg",
  "attribution_table": null,
  "partition_key": "toYYYYMM(event_time)",
  "field_map": {
    "event_time": "event_time",
    "event_type": "event_name",
    "user_id": "visitor_id",
    "session_id": "session_id",
    "utm_source": "utm_source",
    "utm_campaign": "utm_campaign",
    "revenue": "order_revenue",
    "spend_date": "date",
    "spend": "cost",
    "platform": "ad_network"
  }
}
```

---

## ⚡ ClickHouse Performance Best Practices

1. **Always filter on partition key first**: Use `toYYYYMM()` or `toDate()` in WHERE clauses — this enables partition pruning and avoids full-table scans.
2. **Use `uniq()` not `COUNT(DISTINCT)`**: ClickHouse's `uniq()` is ~10x faster for cardinality estimation.
3. **Prefer `countIf()` and `sumIf()`** over sub-queries for conditional aggregation.
4. **Avoid `JOIN` on large event tables**: Prefer pre-aggregated tables when available.
5. **Use `LIMIT` on exploratory queries**: Always add `LIMIT 1000` when doing schema discovery.
6. **Check `system.query_log`** if a query is unexpectedly slow:

```sql
SELECT query, read_rows, read_bytes, query_duration_ms
FROM system.query_log
WHERE type = 'QueryFinish'
  AND event_date = today()
ORDER BY query_duration_ms DESC
LIMIT 10;
```

---

## ⚠️ Query Safety Rules

1. **Read-Only Only**: Only `SELECT` statements are permitted. Never execute mutations (`ALTER TABLE ... UPDATE/DELETE`), `DROP`, `TRUNCATE`, or `INSERT`.
2. **Mandatory date filter**: Every query against event tables MUST include a date range filter on the partition key column.
3. **Max rows guard**: Add `LIMIT 1000000` to any query that might return unbounded rows.
4. **No credential exposure**: Never output `CLICKHOUSE_PASSWORD` or connection strings in any response.
5. **Validate date inputs**: Ensure all date parameters match `YYYY-MM-DD` format before building queries.

---

## 📤 Output Formatting

- Use markdown tables for all results.
- Currency columns: 2 decimal places with currency symbol.
- Rate/percentage columns: 1 decimal place with `%` suffix.
- Large numbers: use thousands separator (e.g., `1,234,567`).
- Always include a "Change vs. Prior Period" column with positive changes in green (⬆️) and negative in red (⬇️) indicators.
