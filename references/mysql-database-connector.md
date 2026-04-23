---
name: mysql-database-connector
version: 1.1.0
description: Connect to a customer's read-only MySQL database. Works with restricted accounts that only have SELECT privilege on specific views or tables.
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
| `MYSQL_USER` | ✅ Yes | — | Database username (read-only account) |
| `MYSQL_PASSWORD` | ✅ Yes | — | Database password (store as secret, never log) |
| `MYSQL_DATABASE` | ✅ Yes | — | Target database name |
| `MYSQL_ALLOWED_TABLES` | ✅ Yes | — | Comma-separated list of table/view names this account can access. Example: `v_orders,v_customers,v_ad_spend` |
| `MYSQL_SSL` | Optional | `false` | Enable SSL/TLS (`true` / `false`) |
| `MYSQL_TIMEZONE` | Optional | `UTC` | Timezone for timestamp parsing |

**Access Model:** The database account is read-only and scoped to the tables/views listed in `MYSQL_ALLOWED_TABLES`. Only `SELECT` is permitted. Schema enumeration via `information_schema` may return partial or no results depending on the account's GRANT scope — always use the declared table list as the source of truth.

**Security Note:** Never echo or expose `MYSQL_PASSWORD` in any output, log, or response. All queries MUST use parameterized placeholders — never interpolate user-provided strings directly into SQL.

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

**Important:** Also check that `MYSQL_ALLOWED_TABLES` is set. If it is empty or unset, halt and ask the user: _"Please set MYSQL_ALLOWED_TABLES to the comma-separated names of the tables or views your database account can access (e.g., `v_orders,v_customers`)."_

---

## 🗺️ Step 2: Schema Discovery (Restricted-Access Mode)

**The account is read-only and scoped. Do NOT attempt to enumerate `information_schema` broadly — it may fail or return empty results for restricted users.**

Instead, use the table list in `MYSQL_ALLOWED_TABLES` and call `DESCRIBE` on each name directly.

### 2.1 Verify Each Declared Table is Accessible

For each table name `T` in `MYSQL_ALLOWED_TABLES`, run:

```sql
-- Replace {T} with each table/view name from MYSQL_ALLOWED_TABLES
DESCRIBE {T};
```

This returns columns, types, nullability, and key info without requiring any `information_schema` privilege. If a table raises a permission error or does not exist, report it to the user and remove it from the working table list.

### 2.2 Inspect Column Details (if DESCRIBE is insufficient)

For views, `DESCRIBE` may omit comments. As a fallback, try querying `information_schema.COLUMNS` for the specific table — but only if the account has `SELECT` privilege on `information_schema`. If it raises a permission error, rely solely on the `DESCRIBE {T}` output from Step 2.1 and ask the user to clarify any ambiguous columns.

---

## 📐 Step 3: Schema Reference

All table and column definitions are declared in a **dedicated schema file**:

**→ [references/mysql-schema.md](mysql-schema.md)**

OpenClaw MUST load and read `mysql-schema.md` before constructing any query.

- If `mysql-schema.md` contains no declared tables yet (still shows `???` placeholders), halt and say: _"The MySQL schema has not been configured yet. Please ask the skill owner to fill in `references/mysql-schema.md` with the real table and column definitions before proceeding."_
- Use only table names and column names found in that file. Never guess.
- Respect the `date_key`, `pk`, and `nullable` annotations when building WHERE clauses and aggregations.

---

## 📊 Step 4: Analysis Queries

Use only the views and columns declared in [references/mysql-schema.md](mysql-schema.md).

### 4.1 Routing Rule: When To Use MySQL vs ClickHouse

MySQL is the customer-facing dimension, detail, and configuration layer. ClickHouse is the customer-facing fact and attribution layer.

For questions such as:

- which campaigns produced orders
- which products were actually sold by each campaign
- what touchpoints or paths led to each order
- campaign-level attributed order or revenue analysis

OpenClaw SHOULD prefer the ClickHouse connector first, because those analyses depend on event facts, attribution paths, and order-product arrays already modeled in ClickHouse.

Use the MySQL connector only when the task needs business detail that is not present in ClickHouse, such as:

- supplementary order-item attributes from `analytics_order_items`
- product or variant dimension lookups from `analytics_products` or `analytics_variants`
- CRM or configuration metadata from `analytics_crm_*`, `analytics_conversion_*`, or exclusion views

If the user asks a campaign-to-product question and the needed fields are available in ClickHouse, do not start from MySQL.

---

## 🔄 Step 5: Schema Mapping Confirmation Flow

When first accessing a customer's database:

1. **Read `MYSQL_ALLOWED_TABLES`** — this is the authoritative list of accessible tables/views.
2. **Load `mysql-schema.md`** — check if tables are already declared.
3. **If `mysql-schema.md` is complete**, use it directly and proceed to Step 4.
4. **If `mysql-schema.md` is not yet filled in**:
   a. Run `DESCRIBE {T}` for each table in `MYSQL_ALLOWED_TABLES`.
   b. Present the column list to the user and ask them to confirm the role of each table and the purpose of key columns.
   c. Do not guess or infer. Ask the skill owner to record the confirmed mapping into `mysql-schema.md`.
5. **Do not proceed to query construction** until `mysql-schema.md` is filled in.

---

## ⚠️ Query Safety Rules

1. **Read-Only by account**: The database account only has `SELECT` privilege. Do not attempt `INSERT`, `UPDATE`, `DELETE`, `DROP`, `ALTER`, or `TRUNCATE` — they will be rejected and must never be generated.
2. **Scope to allowed tables only**: Only query tables/views listed in `MYSQL_ALLOWED_TABLES`. Never try to query tables not in that list, even if the user requests it.
3. **Always use date filters**: Every query against a data table must include a `WHERE` clause with a date range on an indexed column to prevent full-table scans.
4. **LIMIT on exploratory queries**: Add `LIMIT 1000` to any schema-level or sample query.
5. **No raw credential exposure**: Never output connection strings, passwords, or API keys.
6. **Parameterized inputs**: All date ranges and filter values from user input must be validated as proper `YYYY-MM-DD` strings before interpolation.
7. **Do not use MySQL as the default attribution engine**: If the request is fundamentally about campaign attribution, conversion paths, or campaign-to-product outcomes, route to ClickHouse first and use MySQL only as a secondary lookup layer when a required business field is missing there.

---

## 📤 Output Formatting

When returning query results for analysis, format as markdown tables with:
- Numeric columns right-aligned
- Currency values with 2 decimal places and currency symbol
- Date columns in `YYYY-MM-DD` format
- Percentage values with `%` suffix and 1 decimal place
- Always show comparison columns side-by-side: `This Week | Last Week | Change%`
