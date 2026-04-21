---
name: mysql-schema
version: 1.0.0
description: Declares the exact tables and fields accessible via the MySQL read-only account. OpenClaw reads this file to construct all SQL queries. Must be filled in by the skill owner before any query is executed.
---

# MySQL Schema Declaration

> **How to fill in this file:**
> 1. For each table in `MYSQL_ALLOWED_TABLES`, run `DESCRIBE <table_name>;` in your MySQL client.
> 2. Copy the output columns into the corresponding table block below.
> 3. Mark the role of each table (`orders` / `customers` / `order_items` / `ad_spend` / `other`).
> 4. For each column, fill in: exact column name, data type, and a brief description of what it contains.
> 5. Mark the primary date/filter column as `date_key: true` — OpenClaw will always filter on this column.
> 6. Mark the primary key column as `pk: true`.
> 7. Mark columns that are nullable or may be empty as `nullable: true`.

---

## Table Definitions

<!--
  Add one block per accessible table/view.
  Copy the template below and repeat for each table.
-->

### Template (copy and fill in for each table)

```yaml
- table: <exact_table_or_view_name>
  role: <orders|customers|order_items|ad_spend|other>
  description: <one line describing what this table contains>
  columns:
    - name: <column_name>
      type: <INT|BIGINT|VARCHAR|TEXT|DECIMAL|DATETIME|DATE|TINYINT|...>
      description: <what this column represents>
      pk: true           # remove this line if not primary key
      date_key: true     # remove this line if not the main date filter column
      nullable: true     # remove this line if NOT NULL
```

---

## Declared Tables

*(Fill in one block per table below. Remove this line when done.)*

---

## Query Constraints

> OpenClaw MUST follow these rules when building queries against this schema:

- Only use tables and columns declared above. Never guess or add columns not listed here.
- Always filter on the column marked `date_key: true` for any date-range query.
- Never query a table not listed in this file, even if the user requests it.
- For JOIN operations, only join on columns that share the same logical key (e.g., `order_id`).
- If the user asks for a metric that requires a column not present in this schema, respond: _"That field is not available in the current schema. Please check if it needs to be added to the allowed table list."_
