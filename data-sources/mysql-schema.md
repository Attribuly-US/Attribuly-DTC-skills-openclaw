---
name: mysql-schema
version: 1.1.0
description: Declares the customer-facing MySQL analytics views approved for read-only access. OpenClaw reads this file to construct all SQL queries.
runtime_scope: allyclaw_hosted_only
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

```yaml
- table: analytics_orders
  role: orders
  description: Customer-facing order analytics view.
  columns:
    - name: order_id
      type: VARCHAR
      description: Order ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: customer_id
      type: VARCHAR
      description: Customer ID.
    - name: order_number
      type: VARCHAR
      description: Order number.
    - name: order_name
      type: VARCHAR
      description: Order name.
    - name: order_type
      type: TINYINT
      description: Order type such as new or repeat order.
    - name: order_status
      type: TINYINT
      description: Order status.
    - name: financial_status
      type: VARCHAR
      description: Financial status.
    - name: fulfillment_status
      type: VARCHAR
      description: Fulfillment status.
    - name: order_currency
      type: VARCHAR
      description: Order currency.
    - name: gross_revenue_usd
      type: DECIMAL
      description: Gross order revenue in USD.
    - name: net_revenue_usd
      type: DECIMAL
      description: Net order revenue in USD.
    - name: refunded_amount_usd
      type: DECIMAL
      description: Refunded amount in USD.
    - name: discount_amount_usd
      type: DECIMAL
      description: Discount amount in USD.
    - name: tax_amount_usd
      type: DECIMAL
      description: Tax amount in USD.
    - name: shipping_amount_usd
      type: DECIMAL
      description: Shipping amount in USD.
    - name: total_item_quantity
      type: INT
      description: Total order item quantity.
    - name: sales_channel_name
      type: VARCHAR
      description: Sales channel name.
    - name: sales_channel_code
      type: VARCHAR
      description: Sales channel code.
    - name: order_created_at
      type: DATETIME
      description: Order created timestamp.
      date_key: true
    - name: order_updated_at
      type: DATETIME
      description: Order updated timestamp.
      nullable: true
    - name: order_refunded_at
      type: DATETIME
      description: Order refunded timestamp.
      nullable: true
    - name: is_test_order
      type: TINYINT
      description: Whether the order is a test order.

- table: analytics_order_items
  role: order_items
  description: Customer-facing order item analytics view.
  columns:
    - name: order_item_id
      type: VARCHAR
      description: Order item ID.
      pk: true
    - name: order_id
      type: VARCHAR
      description: Order ID.
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: customer_id
      type: VARCHAR
      description: Customer ID.
    - name: product_id
      type: VARCHAR
      description: Product ID.
    - name: variant_id
      type: VARCHAR
      description: Variant ID.
    - name: sku
      type: VARCHAR
      description: SKU.
      nullable: true
    - name: product_name
      type: VARCHAR
      description: Product name.
      nullable: true
    - name: variant_name
      type: VARCHAR
      description: Variant title.
      nullable: true
    - name: quantity_sold
      type: INT
      description: Sold quantity.
    - name: unit_price_usd
      type: DECIMAL
      description: Unit price in USD.
      nullable: true
    - name: gross_item_revenue_usd
      type: DECIMAL
      description: Gross item revenue in USD.
      nullable: true
    - name: item_discount_amount_usd
      type: DECIMAL
      description: Discount amount in USD.
      nullable: true
    - name: item_refunded_amount_usd
      type: DECIMAL
      description: Refunded amount in USD.
      nullable: true
    - name: item_tax_amount_usd
      type: DECIMAL
      description: Item tax amount in USD.
    - name: unit_cost_usd
      type: DECIMAL
      description: Unit cost in USD.
    - name: order_created_at
      type: DATETIME
      description: Parent order created timestamp.
      date_key: true
      nullable: true

- table: analytics_order_refunds
  role: orders
  description: Order refund summary view.
  columns:
    - name: refund_id
      type: VARCHAR
      description: Refund ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: order_id
      type: VARCHAR
      description: Order ID.
    - name: platform_user_id
      type: VARCHAR
      description: Platform user ID associated with the refund.
    - name: refund_note
      type: VARCHAR
      description: Refund note.
      nullable: true
    - name: is_restocked
      type: TINYINT
      description: Whether refunded items were restocked.
      nullable: true
    - name: refund_amount_usd
      type: DECIMAL
      description: Refund amount in USD.
      nullable: true
    - name: refund_created_at
      type: DATETIME
      description: Refund created timestamp.
      date_key: true
      nullable: true
    - name: refund_processed_at
      type: DATETIME
      description: Refund processed timestamp.
      nullable: true

- table: analytics_order_refund_line_items
  role: order_items
  description: Refund line item detail view.
  columns:
    - name: refund_line_item_id
      type: VARCHAR
      description: Refund line item ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: order_id
      type: VARCHAR
      description: Order ID.
    - name: order_item_id
      type: VARCHAR
      description: Order item ID.
    - name: refund_id
      type: VARCHAR
      description: Refund ID.
    - name: refunded_quantity
      type: INT
      description: Refunded quantity.
    - name: restock_type
      type: VARCHAR
      description: Restock type.
      nullable: true
    - name: refund_currency
      type: VARCHAR
      description: Refund currency.
      nullable: true
    - name: refund_subtotal_usd
      type: DECIMAL
      description: Refund subtotal in USD.
      nullable: true
    - name: refund_tax_usd
      type: DECIMAL
      description: Refund tax amount in USD.
      nullable: true
    - name: refund_line_created_at
      type: DATETIME
      description: Refund line created timestamp.
      date_key: true
      nullable: true

- table: analytics_order_refund_adjustments
  role: orders
  description: Refund adjustment detail view.
  columns:
    - name: refund_adjustment_id
      type: BIGINT
      description: Refund adjustment ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: order_id
      type: BIGINT
      description: Order ID.
    - name: refund_id
      type: BIGINT
      description: Refund ID.
    - name: adjustment_type
      type: VARCHAR
      description: Adjustment type.
    - name: adjustment_reason
      type: VARCHAR
      description: Adjustment reason.
    - name: adjustment_currency
      type: VARCHAR
      description: Adjustment currency.
      nullable: true
    - name: adjustment_amount_usd
      type: DECIMAL
      description: Adjustment amount in USD.
      nullable: true
    - name: adjustment_tax_usd
      type: DECIMAL
      description: Adjustment tax in USD.
      nullable: true
    - name: adjustment_created_at
      type: DATETIME
      description: Adjustment created timestamp.
      date_key: true
      nullable: true

- table: analytics_order_tags
  role: other
  description: Order tag mapping view.
  columns:
    - name: order_id
      type: VARCHAR
      description: Order ID.
    - name: order_name
      type: VARCHAR
      description: Order name.
    - name: order_tag
      type: VARCHAR
      description: Order tag.
    - name: tag_created_at
      type: DATETIME
      description: Tag created timestamp.
      date_key: true
      nullable: true

- table: analytics_order_transactions
  role: other
  description: Order payment transaction payload view.
  columns:
    - name: order_id
      type: VARCHAR
      description: Order ID.
      pk: true
    - name: transaction_payload
      type: JSON
      description: Transaction payload JSON.
      nullable: true
    - name: transaction_created_at
      type: DATETIME
      description: Transaction created timestamp.
      date_key: true
      nullable: true

- table: analytics_daily_reports
  role: other
  description: Daily shop summary report view.
  columns:
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: report_date
      type: DATE
      description: Report date.
      date_key: true
    - name: report_currency
      type: VARCHAR
      description: Report currency.
    - name: new_customer_count
      type: INT
      description: New customer count.
    - name: purchasing_customer_count
      type: INT
      description: Purchasing customer count.
      nullable: true
    - name: repeat_customer_count
      type: INT
      description: Repeat customer count.
      nullable: true
    - name: new_order_count
      type: INT
      description: New order count.
    - name: repeat_order_count
      type: INT
      description: Repeat order count.
    - name: new_order_revenue_usd
      type: DECIMAL
      description: New order revenue in USD.
    - name: repeat_order_revenue_usd
      type: DECIMAL
      description: Repeat order revenue in USD.

- table: analytics_customers
  role: customers
  description: Customer analytics view.
  columns:
    - name: customer_id
      type: VARCHAR
      description: Customer ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: contact_id
      type: BIGINT
      description: CRM contact ID.
    - name: customer_state
      type: VARCHAR
      description: Customer state.
    - name: lifetime_order_count
      type: INT
      description: Lifetime order count.
    - name: lifetime_spend_amount
      type: DECIMAL
      description: Lifetime spend amount.
    - name: customer_currency
      type: VARCHAR
      description: Customer currency.
    - name: accepts_marketing
      type: TINYINT
      description: Whether the customer accepts marketing.
    - name: marketing_opt_in_level
      type: VARCHAR
      description: Marketing opt-in level.
      nullable: true
    - name: is_email_verified
      type: INT
      description: Whether email is verified.
    - name: last_order_id
      type: BIGINT
      description: Last order ID.
    - name: last_order_name
      type: VARCHAR
      description: Last order name.
    - name: customer_created_at
      type: DATETIME
      description: Customer created timestamp.
      date_key: true
      nullable: true

- table: analytics_customer_tags
  role: customers
  description: Customer tag mapping view.
  columns:
    - name: customer_id
      type: VARCHAR
      description: Customer ID.
    - name: contact_id
      type: INT
      description: CRM contact ID.
    - name: customer_tag
      type: VARCHAR
      description: Customer tag.
    - name: tag_created_at
      type: DATETIME
      description: Tag created timestamp.
      date_key: true
      nullable: true

- table: analytics_products
  role: other
  description: Product analytics view.
  columns:
    - name: product_id
      type: VARCHAR
      description: Product ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: product_title
      type: VARCHAR
      description: Product title.
      nullable: true
    - name: vendor_name
      type: VARCHAR
      description: Vendor name.
    - name: product_type
      type: VARCHAR
      description: Product type.
    - name: product_status
      type: VARCHAR
      description: Product status.
    - name: product_handle
      type: VARCHAR
      description: Product handle.
      nullable: true
    - name: product_tags
      type: VARCHAR
      description: Product tags.
    - name: primary_image_url
      type: VARCHAR
      description: Primary image URL.
    - name: product_created_at
      type: DATETIME
      description: Product created timestamp.
      date_key: true
      nullable: true
    - name: product_published_at
      type: DATETIME
      description: Product published timestamp.
      nullable: true

- table: analytics_variants
  role: other
  description: Product variant analytics view.
  columns:
    - name: variant_id
      type: VARCHAR
      description: Variant ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: product_id
      type: VARCHAR
      description: Product ID.
    - name: product_name
      type: VARCHAR
      description: Product name.
      nullable: true
    - name: variant_title
      type: VARCHAR
      description: Variant title.
    - name: sku
      type: VARCHAR
      description: SKU.
    - name: variant_currency
      type: VARCHAR
      description: Variant currency.
    - name: selling_price_usd
      type: DECIMAL
      description: Selling price in USD.
    - name: compare_at_price_usd
      type: DECIMAL
      description: Compare-at price in USD.
    - name: inventory_quantity
      type: INT
      description: Inventory quantity.
      nullable: true
    - name: variant_created_at
      type: DATETIME
      description: Variant created timestamp.
      date_key: true
      nullable: true

- table: analytics_inventory_items
  role: other
  description: Inventory cost view.
  columns:
    - name: inventory_item_id
      type: VARCHAR
      description: Inventory item ID.
      pk: true
    - name: variant_id
      type: BIGINT
      description: Variant ID.
    - name: unit_cost_usd
      type: DECIMAL
      description: Unit cost in USD.
    - name: platform_created_at
      type: DATETIME
      description: Platform creation timestamp.
      date_key: true

- table: analytics_discount_codes
  role: other
  description: Discount code analytics view.
  columns:
    - name: discount_id
      type: BIGINT
      description: Discount ID.
      pk: true
    - name: shop_id
      type: BIGINT
      description: Shop ID.
    - name: discount_title
      type: VARCHAR
      description: Discount title.
    - name: discount_summary
      type: VARCHAR
      description: Discount summary.
    - name: discount_class
      type: VARCHAR
      description: Discount class.
    - name: applies_once_per_customer
      type: TINYINT
      description: Whether the discount applies once per customer.
    - name: discount_status
      type: VARCHAR
      description: Discount status.
    - name: discount_code
      type: VARCHAR
      description: Discount code text.
    - name: starts_at
      type: DATETIME
      description: Discount start timestamp.
      date_key: true
      nullable: true
    - name: ends_at
      type: DATETIME
      description: Discount end timestamp.
      nullable: true

- table: analytics_shops
  role: other
  description: Shop dimension view.
  columns:
    - name: shop_id
      type: BIGINT
      description: Shop ID.
      pk: true
    - name: team_id
      type: INT
      description: Team ID.
    - name: shop_name
      type: VARCHAR
      description: Shop name.
    - name: shop_type
      type: TINYINT
      description: Shop type.
    - name: primary_domain
      type: VARCHAR
      description: Primary domain.
    - name: platform_domain
      type: VARCHAR
      description: Platform domain.
    - name: shop_currency
      type: VARCHAR
      description: Shop currency.
    - name: product_count
      type: INT
      description: Product count.
    - name: customer_count
      type: INT
      description: Customer count.
    - name: order_count
      type: INT
      description: Order count.
    - name: trailing_year_sales_amount
      type: DECIMAL
      description: Trailing year sales amount.
    - name: shop_status
      type: TINYINT
      description: Shop status.
    - name: shop_created_at
      type: DATETIME
      description: Shop created timestamp.
      date_key: true
      nullable: true

- table: analytics_ad_accounts
  role: ad_spend
  description: Ad account dimension view.
  columns:
    - name: ad_account_pk
      type: INT
      description: Internal ad account primary key.
      pk: true
    - name: ad_account_id
      type: VARCHAR
      description: Platform ad account ID.
    - name: ad_account_name
      type: VARCHAR
      description: Ad account name.
    - name: ad_platform
      type: VARCHAR
      description: Ad platform.
    - name: connection_status
      type: INT
      description: Connection status.
    - name: account_status
      type: INT
      description: Account status.
    - name: account_currency
      type: CHAR
      description: Account currency.
    - name: account_created_at
      type: DATETIME
      description: Account created timestamp.
      date_key: true
    - name: credential_expires_at
      type: DATETIME
      description: Credential expiration timestamp.
      nullable: true

- table: analytics_ad_campaigns
  role: ad_spend
  description: Ad campaign dimension view.
  columns:
    - name: ad_campaign_pk
      type: INT
      description: Internal campaign primary key.
      pk: true
    - name: campaign_id
      type: VARCHAR
      description: Platform campaign ID.
    - name: ad_account_pk
      type: INT
      description: Ad account primary key.
    - name: campaign_name
      type: VARCHAR
      description: Campaign name.
    - name: campaign_type
      type: VARCHAR
      description: Campaign type.
    - name: campaign_objective
      type: VARCHAR
      description: Campaign objective.
    - name: campaign_status
      type: INT
      description: Campaign status.
    - name: daily_budget_amount
      type: DECIMAL
      description: Daily budget amount.
    - name: lifetime_budget_amount
      type: DECIMAL
      description: Lifetime budget amount.
    - name: platform_created_at
      type: DATETIME
      description: Campaign platform creation timestamp.
      date_key: true
      nullable: true

- table: analytics_ad_sets
  role: ad_spend
  description: Ad set dimension view.
  columns:
    - name: ad_set_pk
      type: INT
      description: Internal ad set primary key.
      pk: true
    - name: ad_set_id
      type: VARCHAR
      description: Platform ad set ID.
    - name: ad_campaign_pk
      type: INT
      description: Internal campaign primary key.
    - name: ad_account_id
      type: VARCHAR
      description: Ad account ID.
    - name: ad_set_name
      type: VARCHAR
      description: Ad set name.
    - name: ad_set_status
      type: INT
      description: Ad set status.
    - name: daily_budget_amount
      type: DECIMAL
      description: Daily budget amount.
    - name: platform_created_at
      type: DATETIME
      description: Ad set platform creation timestamp.
      date_key: true
      nullable: true

- table: analytics_ads
  role: ad_spend
  description: Ad dimension view.
  columns:
    - name: ad_pk
      type: INT
      description: Internal ad primary key.
      pk: true
    - name: ad_id
      type: VARCHAR
      description: Platform ad ID.
    - name: ad_set_pk
      type: BIGINT
      description: Internal ad set primary key.
    - name: ad_campaign_pk
      type: INT
      description: Internal campaign primary key.
    - name: ad_account_id
      type: VARCHAR
      description: Ad account ID.
    - name: ad_name
      type: VARCHAR
      description: Ad name.
    - name: ad_platform
      type: VARCHAR
      description: Ad platform.
    - name: ad_status
      type: INT
      description: Ad status.
    - name: creative_reference
      type: VARCHAR
      description: Creative reference.
    - name: platform_created_at
      type: DATETIME
      description: Ad platform creation timestamp.
      date_key: true
      nullable: true

- table: analytics_ad_materials
  role: ad_spend
  description: Ad material view.
  columns:
    - name: material_pk
      type: INT
      description: Internal material primary key.
      pk: true
    - name: material_id
      type: VARCHAR
      description: Source material ID.
    - name: ad_account_pk
      type: INT
      description: Ad account primary key.
    - name: material_type
      type: VARCHAR
      description: Material type such as image or video.
    - name: material_title
      type: VARCHAR
      description: Material title.
    - name: image_url
      type: VARCHAR
      description: Image URL.
    - name: thumbnail_url
      type: VARCHAR
      description: Thumbnail URL.
    - name: video_url
      type: VARCHAR
      description: Video URL.
    - name: platform_created_at
      type: DATETIME
      description: Platform material creation timestamp.
      date_key: true
      nullable: true

- table: analytics_ad_creative_materials
  role: other
  description: Creative-to-material bridge view.
  columns:
    - name: creative_material_pk
      type: INT
      description: Internal bridge row primary key.
      pk: true
    - name: creative_id
      type: VARCHAR
      description: Creative ID.
    - name: material_pk
      type: INT
      description: Material primary key.
    - name: material_source_id
      type: VARCHAR
      description: Material source ID.
    - name: record_created_at
      type: DATETIME
      description: Bridge row creation timestamp.
      date_key: true
      nullable: true

- table: analytics_impact_actions
  role: other
  description: Impact affiliate action analytics view.
  columns:
    - name: action_id
      type: VARCHAR
      description: Affiliate action ID.
      pk: true
    - name: ad_account_pk
      type: INT
      description: Ad account primary key.
    - name: campaign_id
      type: VARCHAR
      description: Campaign ID.
    - name: ad_pk
      type: INT
      description: Ad primary key.
    - name: partner_order_id
      type: VARCHAR
      description: Partner order ID.
    - name: order_id
      type: VARCHAR
      description: Internal order ID.
    - name: payout_amount
      type: DECIMAL
      description: Payout amount.
    - name: action_at
      type: DATETIME
      description: Action timestamp.
      date_key: true

- table: analytics_impact_clicks
  role: other
  description: Impact affiliate click analytics view.
  columns:
    - name: click_id
      type: VARCHAR
      description: Affiliate click ID.
      pk: true
    - name: campaign_id
      type: VARCHAR
      description: Campaign ID.
    - name: campaign_name
      type: VARCHAR
      description: Campaign name.
    - name: ad_id
      type: VARCHAR
      description: Ad ID.
    - name: ad_name
      type: VARCHAR
      description: Ad name.
    - name: click_at
      type: DATETIME
      description: Click timestamp.
      date_key: true
      nullable: true

- table: analytics_share_a_sale_actions
  role: other
  description: ShareASale affiliate action analytics view.
  columns:
    - name: action_id
      type: VARCHAR
      description: Affiliate action ID.
      pk: true
    - name: ad_account_pk
      type: INT
      description: Ad account primary key.
    - name: campaign_id
      type: VARCHAR
      description: Campaign ID.
    - name: ad_pk
      type: INT
      description: Ad primary key.
    - name: order_id
      type: VARCHAR
      description: Internal order ID.
    - name: payout_amount
      type: DECIMAL
      description: Payout amount.
    - name: action_at
      type: DATETIME
      description: Action timestamp.
      date_key: true

- table: analytics_crm_contacts
  role: customers
  description: CRM contact analytics view.
  columns:
    - name: crm_contact_id
      type: INT
      description: CRM contact ID.
      pk: true
    - name: crm_company_id
      type: INT
      description: CRM company ID.
      nullable: true
    - name: crm_manager_id
      type: INT
      description: CRM manager ID.
      nullable: true
    - name: crm_stage_id
      type: INT
      description: CRM stage ID.
      nullable: true
    - name: shopify_customer_id
      type: VARCHAR
      description: Shopify customer ID.
      nullable: true
    - name: contact_name
      type: VARCHAR
      description: Contact name.
      nullable: true
    - name: job_title
      type: VARCHAR
      description: Job title.
      nullable: true
    - name: country_name
      type: VARCHAR
      description: Country name.
      nullable: true
    - name: province_name
      type: VARCHAR
      description: Province name.
      nullable: true
    - name: city_name
      type: VARCHAR
      description: City name.
      nullable: true
    - name: accepts_marketing
      type: TINYINT
      description: Whether the contact accepts marketing.
    - name: completed_order_count
      type: INT
      description: Completed order count.
    - name: lead_score
      type: INT
      description: Lead score.
    - name: last_active_at
      type: DATETIME
      description: Last active timestamp.
      nullable: true
    - name: contact_created_at
      type: DATETIME
      description: Contact creation timestamp.
      date_key: true

- table: analytics_crm_contact_sources
  role: customers
  description: CRM contact source mapping view.
  columns:
    - name: crm_contact_id
      type: BIGINT
      description: CRM contact ID.
    - name: source_system
      type: VARCHAR
      description: Source system.
    - name: source_channel
      type: VARCHAR
      description: Parent source or channel.
    - name: source_created_at
      type: DATETIME
      description: Source mapping creation timestamp.
      date_key: true

- table: analytics_crm_attributes
  role: other
  description: CRM attribute dictionary view.
  columns:
    - name: crm_attribute_id
      type: INT
      description: CRM attribute ID.
      pk: true
    - name: object_type
      type: VARCHAR
      description: Object type such as contact or company.
    - name: attribute_name
      type: VARCHAR
      description: Attribute display name.
    - name: storage_column_name
      type: VARCHAR
      description: Underlying storage column name.
    - name: attribute_type
      type: VARCHAR
      description: Attribute type.
    - name: is_system_attribute
      type: TINYINT
      description: Whether the attribute is a system attribute.
      nullable: true
    - name: attribute_description
      type: TEXT
      description: Attribute description.
      nullable: true
    - name: hide_type
      type: VARCHAR
      description: Hide type.
      nullable: true
    - name: attribute_created_at
      type: DATETIME
      description: Attribute creation timestamp.
      date_key: true

- table: analytics_crm_attribute_options
  role: other
  description: CRM attribute option dictionary view.
  columns:
    - name: crm_attribute_option_id
      type: INT
      description: CRM attribute option ID.
      pk: true
    - name: crm_attribute_id
      type: INT
      description: CRM attribute ID.
    - name: option_name
      type: VARCHAR
      description: Option name.
    - name: option_created_at
      type: DATETIME
      description: Option creation timestamp.
      date_key: true

- table: analytics_conversion_views
  role: other
  description: Saved conversion analysis view definitions.
  columns:
    - name: view_code
      type: CHAR
      description: View code.
      pk: true
    - name: view_name
      type: VARCHAR
      description: View name.
    - name: view_description
      type: VARCHAR
      description: View description.
    - name: analysis_scene
      type: VARCHAR
      description: Analysis scene.
    - name: view_definition
      type: JSON
      description: Saved view definition payload.
    - name: view_version
      type: INT
      description: View version.
    - name: view_created_at
      type: DATETIME
      description: View creation timestamp.
      date_key: true

- table: analytics_conversion_goals
  role: other
  description: Conversion goal configuration view.
  columns:
    - name: goal_code
      type: CHAR
      description: Goal code.
      pk: true
    - name: goal_name
      type: VARCHAR
      description: Goal name.
    - name: event_type
      type: VARCHAR
      description: Event type mapped to the goal.
    - name: goal_description
      type: VARCHAR
      description: Goal description.
    - name: limit_type
      type: VARCHAR
      description: Goal limit type.
    - name: limit_value
      type: JSON
      description: Goal limit value payload.
      nullable: true
    - name: goal_created_at
      type: DATETIME
      description: Goal creation timestamp.
      date_key: true

- table: analytics_customer_exclusions
  role: other
  description: Customer tag exclusion configuration view.
  columns:
    - name: exclusion_code
      type: CHAR
      description: Exclusion code.
      pk: true
    - name: excluded_customer_tag
      type: VARCHAR
      description: Excluded customer tag.
    - name: exclusion_created_at
      type: DATETIME
      description: Exclusion creation timestamp.
      date_key: true

- table: analytics_order_tag_exclusions
  role: other
  description: Order tag exclusion configuration view.
  columns:
    - name: exclusion_code
      type: CHAR
      description: Exclusion code.
      pk: true
    - name: excluded_order_tag
      type: VARCHAR
      description: Excluded order tag.
    - name: exclusion_created_at
      type: DATETIME
      description: Exclusion creation timestamp.
      date_key: true

- table: analytics_referral_exclusions
  role: other
  description: Referral domain exclusion configuration view.
  columns:
    - name: exclusion_code
      type: CHAR
      description: Exclusion code.
      pk: true
    - name: excluded_referral_domain
      type: VARCHAR
      description: Excluded referral domain.
    - name: exclusion_created_at
      type: DATETIME
      description: Exclusion creation timestamp.
      date_key: true

- table: analytics_sale_channel_exclusions
  role: other
  description: Sale channel exclusion configuration view.
  columns:
    - name: exclusion_code
      type: CHAR
      description: Exclusion code.
      pk: true
    - name: excluded_channel_name
      type: VARCHAR
      description: Excluded channel name.
    - name: excluded_channel_code
      type: VARCHAR
      description: Excluded channel code.
    - name: exclusion_created_at
      type: DATETIME
      description: Exclusion creation timestamp.
      date_key: true

- table: analytics_channel_groups
  role: other
  description: Custom attribution channel group dictionary view.
  columns:
    - name: channel_group_code
      type: CHAR
      description: Channel group code.
      pk: true
    - name: channel_group_name
      type: VARCHAR
      description: Channel group name.
    - name: group_status
      type: VARCHAR
      description: Group status.
    - name: group_created_at
      type: DATETIME
      description: Group creation timestamp.
      date_key: true

- table: analytics_channels
  role: other
  description: Custom attribution channel dictionary view.
  columns:
    - name: channel_code
      type: CHAR
      description: Channel code.
      pk: true
    - name: channel_group_code
      type: CHAR
      description: Channel group code.
    - name: channel_name
      type: VARCHAR
      description: Channel name.
    - name: channel_description
      type: VARCHAR
      description: Channel description.
      nullable: true
    - name: channel_condition
      type: JSON
      description: Channel condition definition.
      nullable: true
    - name: clean_sort_order
      type: TINYINT
      description: Clean sort order.
    - name: channel_created_at
      type: DATETIME
      description: Channel creation timestamp.
      date_key: true

- table: analytics_attribution_models
  role: other
  description: Attribution model configuration view.
  columns:
    - name: model_code
      type: CHAR
      description: Model code.
      pk: true
    - name: model_name
      type: VARCHAR
      description: Model name.
    - name: model_description
      type: VARCHAR
      description: Model description.
    - name: base_model
      type: VARCHAR
      description: Base attribution model.
    - name: attribution_window_days
      type: INT
      description: Attribution window in days.
    - name: scene_scope
      type: JSON
      description: Scene scope JSON.
    - name: included_channels
      type: JSON
      description: Included channels JSON.
    - name: included_ad_platforms
      type: JSON
      description: Included ad platforms JSON.
    - name: channel_mapping
      type: JSON
      description: Channel mapping JSON.
    - name: position_percentage
      type: JSON
      description: Position percentage JSON.
      nullable: true
    - name: model_created_at
      type: DATETIME
      description: Model creation timestamp.
      date_key: true

- table: analytics_campaign_funnel_stages
  role: other
  description: Campaign to funnel stage mapping view.
  columns:
    - name: campaign_name_md5
      type: CHAR
      description: MD5 of campaign name.
      pk: true
    - name: campaign_name
      type: VARCHAR
      description: Campaign name.
    - name: funnel_stage_name
      type: VARCHAR
      description: Funnel stage name.
    - name: mapping_created_at
      type: DATETIME
      description: Mapping creation timestamp.
      date_key: true
```

---

## Query Constraints

> OpenClaw MUST follow these rules when building queries against this schema:

- Only use tables and columns declared above. Never guess or add columns not listed here.
- Always filter on the column marked `date_key: true` for any date-range query.
- Never query a table not listed in this file, even if the user requests it.
- For JOIN operations, only join on columns that share the same logical key (e.g., `order_id`).
- If the user asks for a metric that requires a column not present in this schema, respond: _"That field is not available in the current schema. Please check if it needs to be added to the allowed table list."_
