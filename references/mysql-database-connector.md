---
name: mysql-database-connector
version: 1.0.0
description: Connect to a customer's MySQL database, auto-discover ecommerce tables and fields, and query order/customer/product data for marketing analysis.
---
# Skill: MySQL Database Connector for DTC Ecommerce

## 🎯 Purpose

Enable OpenClaw to read directly from the customer's own MySQL database instead of (or in addition to) platform APIs. Supports Shopify MySQL exports, WooCommerce, custom ecommerce backends, and any MySQL-compatible database (MariaDB, PlanetScale, TiDB, AWS Aurora, etc.).

---

## 🔐 Environment Variables (Connection Setup)

When this skill is activated, the following environment variables MUST be configured by the customer:

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `MYSQL_HOST` | ✅ Yes | — | Database host (IP or domain) |
| `MYSQL_PORT` | Optional | `3306` | TCP port |
| `MYSQL_USER` | ✅ Yes | — | Database username |
| `MYSQL_PASSWORD` | ✅ Yes | — | Database password (store as secret, never log) |
| `MYSQL_DATABASE` | ✅ Yes | — | Target database name |
| `MYSQL_SSL` | Optional | `false` | Enable SSL/TLS (`true` / `false`) |
| `MYSQL_TIMEZONE` | Optional | `UTC` | Timezone for timestamp parsing |

**Security Note:** Never echo or expose `MYSQL_PASSWORD` in any output, log, or response. All connections MUST use parameterized queries — never interpolate user-provided strings into SQL.

---

## 🔌 Step 1: Establish Connection & Validate

Before running any analysis query, verify the connection is available:

```sql
-- Connectivity test
SELECT 1 AS ping;

-- Confirm you are in the right database
SELECT DATABASE() AS current_db;
```

If the connection fails, output a clear error message with the failing variable name (but never the credential value) and ask the user to verify their configuration.

---

## 🗺️ Step 2: Schema Discovery

### 2.1 List All Tables

```sql
SELECT 
  TABLE_NAME,
  TABLE_ROWS,
  ROUND((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024, 2) AS size_mb,
  CREATE_TIME,
  UPDATE_TIME
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = DATABASE()
ORDER BY TABLE_ROWS DESC;
```

### 2.2 Describe a Specific Table

```sql
SELECT 
  COLUMN_NAME,
  COLUMN_TYPE,
  IS_NULLABLE,
  COLUMN_KEY,
  COLUMN_DEFAULT,
  EXTRA,
  COLUMN_COMMENT
FROM information_schema.COLUMNS
WHERE TABLE_SCHEMA = DATABASE()
  AND TABLE_NAME = '{table_name}'
ORDER BY ORDINAL_POSITION;
```

### 2.3 Auto-Detect Ecommerce Table Candidates

Run this to identify likely ecommerce tables by name patterns:

```sql
SELECT TABLE_NAME, TABLE_ROWS
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = DATABASE()
  AND (
    TABLE_NAME REGEXP '(order|purchase|transaction|sale)' OR
    TABLE_NAME REGEXP '(customer|user|member|contact)' OR
    TABLE_NAME REGEXP '(product|item|sku|variant|catalog)' OR
    TABLE_NAME REGEXP '(session|visit|pageview|event|click)' OR
    TABLE_NAME REGEXP '(ad_spend|campaign|attribution|channel)'
  )
ORDER BY TABLE_ROWS DESC;
```

---

## 📐 Step 3: Schema Mapping

After discovering tables, map the customer's actual column names to the **canonical field names** used throughout this skill.

### Orders Table (Required)

Ask the user to confirm or auto-detect by looking for these column patterns:

| Canonical Field | Common MySQL Column Names |
|----------------|--------------------------|
| `order_id` | `id`, `order_id`, `order_number`, `num` |
| `customer_id` | `customer_id`, `user_id`, `buyer_id` |
| `created_at` | `created_at`, `order_date`, `date`, `placed_at`, `purchase_time` |
| `revenue` | `total_price`, `grand_total`, `revenue`, `amount`, `subtotal` |
| `gross_revenue` | `subtotal_price`, `subtotal`, `gross_amount` |
| `discount_amount` | `total_discounts`, `discount`, `coupon_amount` |
| `tax_amount` | `total_tax`, `tax`, `tax_amount` |
| `shipping_amount` | `shipping`, `shipping_cost`, `freight` |
| `cogs` | `cost_of_goods`, `cogs`, `product_cost` |
| `profit` | `profit`, `net_profit`, `margin_amount` |
| `status` | `financial_status`, `status`, `order_status`, `payment_status` |
| `fulfillment_status` | `fulfillment_status`, `shipping_status`, `delivery_status` |
| `currency` | `currency`, `currency_code` |
| `channel` | `channel`, `source`, `utm_source`, `referrer` |
| `utm_source` | `utm_source`, `source` |
| `utm_medium` | `utm_medium`, `medium` |
| `utm_campaign` | `utm_campaign`, `campaign`, `campaign_name` |
| `coupon_code` | `discount_code`, `coupon`, `promo_code` |
| `customer_email` | `email`, `customer_email`, `buyer_email` |

### Order Line Items Table (Optional but Recommended)

| Canonical Field | Common MySQL Column Names |
|----------------|--------------------------|
| `line_item_id` | `id`, `line_item_id` |
| `order_id` | `order_id`, `parent_order_id` |
| `product_id` | `product_id`, `item_id` |
| `variant_id` | `variant_id`, `sku_id` |
| `product_title` | `title`, `name`, `product_name` |
| `sku` | `sku`, `barcode`, `item_code` |
| `quantity` | `quantity`, `qty` |
| `unit_price` | `price`, `unit_price`, `sale_price` |
| `unit_cost` | `cost`, `unit_cost`, `cogs` |
| `line_total` | `total`, `line_total`, `amount` |

### Customers Table (Optional)

| Canonical Field | Common MySQL Column Names |
|----------------|--------------------------|
| `customer_id` | `id`, `customer_id`, `user_id` |
| `email` | `email`, `email_address` |
| `first_name` | `first_name`, `given_name` |
| `last_name` | `last_name`, `family_name` |
| `created_at` | `created_at`, `registered_at`, `signup_date` |
| `orders_count` | `orders_count`, `total_orders`, `purchase_count` |
| `total_spent` | `total_spent`, `lifetime_value`, `ltv` |
| `tags` | `tags`, `labels`, `segment` |
| `is_new_customer` | `is_new`, `first_order`, computed from `orders_count = 1` |

### Ad Spend Table (Optional, if imported from platforms)

| Canonical Field | Common MySQL Column Names |
|----------------|--------------------------|
| `spend_date` | `date`, `report_date`, `spend_date` |
| `platform` | `platform`, `channel`, `source` |
| `campaign_id` | `campaign_id`, `ad_campaign_id` |
| `campaign_name` | `campaign_name`, `campaign` |
| `ad_set_id` | `ad_set_id`, `adset_id`, `ad_group_id` |
| `ad_set_name` | `ad_set_name`, `adset_name`, `ad_group_name` |
| `spend` | `spend`, `cost`, `amount_spent` |
| `impressions` | `impressions`, `views` |
| `clicks` | `clicks`, `link_clicks` |

---

## 📊 Step 4: Core Analysis Queries

Use the mapped column names (aliases to canonical names) in all output queries.

### Query Template Pattern

Always alias to canonical names for downstream consistency:

```sql
SELECT
  {order_id_col}   AS order_id,
  {customer_id_col} AS customer_id,
  {created_at_col} AS created_at,
  {revenue_col}    AS revenue
FROM {orders_table}
WHERE {created_at_col} BETWEEN '{start_date}' AND '{end_date}';
```

---

### Q1: Daily Revenue Summary

```sql
SELECT
  DATE({created_at_col})                        AS report_date,
  COUNT(*)                                       AS orders,
  ROUND(SUM({revenue_col}), 2)                   AS total_revenue,
  ROUND(AVG({revenue_col}), 2)                   AS avg_order_value,
  COUNT(DISTINCT {customer_id_col})              AS unique_customers
FROM {orders_table}
WHERE {status_col} NOT IN ('cancelled', 'refunded', 'void')
  AND DATE({created_at_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY report_date
ORDER BY report_date ASC;
```

### Q2: New vs. Returning Customers (ncROAS Foundation)

```sql
-- Identify new customers (first-ever order)
WITH customer_first_order AS (
  SELECT
    {customer_id_col}          AS customer_id,
    MIN({created_at_col})      AS first_order_date
  FROM {orders_table}
  WHERE {status_col} NOT IN ('cancelled', 'refunded')
  GROUP BY {customer_id_col}
)
SELECT
  DATE(o.{created_at_col})                            AS report_date,
  COUNT(*)                                             AS total_orders,
  SUM(CASE WHEN DATE(o.{created_at_col}) = DATE(cfo.first_order_date) THEN 1 ELSE 0 END) AS new_customer_orders,
  SUM(CASE WHEN DATE(o.{created_at_col}) > DATE(cfo.first_order_date) THEN 1 ELSE 0 END) AS returning_customer_orders,
  ROUND(SUM(CASE WHEN DATE(o.{created_at_col}) = DATE(cfo.first_order_date) THEN o.{revenue_col} ELSE 0 END), 2) AS new_customer_revenue,
  ROUND(SUM(CASE WHEN DATE(o.{created_at_col}) > DATE(cfo.first_order_date) THEN o.{revenue_col} ELSE 0 END), 2) AS returning_customer_revenue
FROM {orders_table} o
JOIN customer_first_order cfo ON o.{customer_id_col} = cfo.customer_id
WHERE o.{status_col} NOT IN ('cancelled', 'refunded')
  AND DATE(o.{created_at_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY report_date
ORDER BY report_date ASC;
```

### Q3: Channel / UTM Attribution Summary

```sql
SELECT
  COALESCE({utm_source_col}, 'direct/unknown')         AS utm_source,
  COALESCE({utm_medium_col}, 'none')                   AS utm_medium,
  COALESCE({utm_campaign_col}, 'none')                 AS utm_campaign,
  COUNT(*)                                              AS orders,
  ROUND(SUM({revenue_col}), 2)                          AS revenue,
  ROUND(AVG({revenue_col}), 2)                          AS aov
FROM {orders_table}
WHERE {status_col} NOT IN ('cancelled', 'refunded')
  AND DATE({created_at_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY utm_source, utm_medium, utm_campaign
ORDER BY revenue DESC
LIMIT 50;
```

### Q4: Customer LTV Calculation

```sql
SELECT
  {customer_id_col}                           AS customer_id,
  MIN({created_at_col})                        AS first_order_date,
  MAX({created_at_col})                        AS last_order_date,
  COUNT(*)                                     AS total_orders,
  ROUND(SUM({revenue_col}), 2)                 AS lifetime_revenue,
  ROUND(AVG({revenue_col}), 2)                 AS avg_order_value,
  DATEDIFF(MAX({created_at_col}), MIN({created_at_col})) AS customer_lifespan_days
FROM {orders_table}
WHERE {status_col} NOT IN ('cancelled', 'refunded')
GROUP BY {customer_id_col}
ORDER BY lifetime_revenue DESC;
```

### Q5: Top Products by Revenue

```sql
SELECT
  li.{product_title_col}                      AS product_name,
  li.{sku_col}                                AS sku,
  SUM(li.{quantity_col})                      AS total_units_sold,
  ROUND(SUM(li.{line_total_col}), 2)          AS total_revenue,
  ROUND(AVG(li.{unit_price_col}), 2)          AS avg_unit_price,
  COUNT(DISTINCT li.{order_id_col})           AS orders_containing
FROM {order_items_table} li
JOIN {orders_table} o ON li.{line_item_order_id_col} = o.{order_id_col}
WHERE o.{status_col} NOT IN ('cancelled', 'refunded')
  AND DATE(o.{created_at_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY product_name, sku
ORDER BY total_revenue DESC
LIMIT 30;
```

### Q6: Weekly WoW Comparison (Revenue & Orders)

```sql
SELECT
  YEARWEEK({created_at_col}, 1)               AS year_week,
  MIN(DATE({created_at_col}))                 AS week_start,
  MAX(DATE({created_at_col}))                 AS week_end,
  COUNT(*)                                    AS orders,
  COUNT(DISTINCT {customer_id_col})           AS unique_customers,
  ROUND(SUM({revenue_col}), 2)                AS total_revenue,
  ROUND(AVG({revenue_col}), 2)                AS aov
FROM {orders_table}
WHERE {status_col} NOT IN ('cancelled', 'refunded')
  AND {created_at_col} >= DATE_SUB(CURDATE(), INTERVAL 56 DAY)
GROUP BY year_week
ORDER BY year_week ASC;
```

### Q7: Profit Analysis (when COGS data is available)

```sql
SELECT
  DATE({created_at_col})                            AS report_date,
  ROUND(SUM({revenue_col}), 2)                       AS revenue,
  ROUND(SUM({cogs_col}), 2)                          AS total_cogs,
  ROUND(SUM({revenue_col}) - SUM({cogs_col}), 2)    AS gross_profit,
  ROUND((SUM({revenue_col}) - SUM({cogs_col})) / NULLIF(SUM({revenue_col}), 0) * 100, 2) AS gross_margin_pct
FROM {orders_table}
WHERE {status_col} NOT IN ('cancelled', 'refunded')
  AND DATE({created_at_col}) BETWEEN '{start_date}' AND '{end_date}'
GROUP BY report_date
ORDER BY report_date ASC;
```

---

## 🔄 Step 5: Schema Mapping Confirmation Flow

When first accessing a customer's database, always run this confirmation flow:

1. **Discover tables** using Step 2 queries.
2. **Present matches** to the customer with a table showing detected table → canonical name.
3. **Confirm or override**: Ask "I found these tables. Do you want to override any mapping?"
4. **Persist mapping** in conversation context as a JSON map:

```json
{
  "orders_table": "shopify_orders",
  "order_items_table": "shopify_order_items",
  "customers_table": "shopify_customers",
  "ad_spend_table": null,
  "field_map": {
    "order_id": "id",
    "created_at": "created_at",
    "revenue": "total_price",
    "status": "financial_status",
    "customer_id": "customer_id",
    "utm_source": "referring_site",
    "utm_campaign": "landing_site"
  }
}
```

---

## ⚠️ Query Safety Rules

1. **Read-Only Only**: Only `SELECT` statements are permitted. Never execute `INSERT`, `UPDATE`, `DELETE`, `DROP`, `ALTER`, or `TRUNCATE`.
2. **Always use date filters**: Prevent full-table scans by always including a `WHERE` clause with date range on an indexed column.
3. **LIMIT on discovery queries**: Add `LIMIT 1000` to any exploratory query not using aggregation.
4. **No raw credential exposure**: Never output connection strings, passwords, or API keys.
5. **Parameterized inputs**: All date ranges and filter values from user input must be validated as proper date strings (`YYYY-MM-DD`) before interpolation.

---

## 📤 Output Formatting

When returning query results for analysis, format as markdown tables with:
- Numeric columns right-aligned
- Currency values with 2 decimal places and currency symbol
- Date columns in `YYYY-MM-DD` format
- Percentage values with `%` suffix and 1 decimal place
- Always show comparison columns side-by-side: `This Week | Last Week | Change%`
