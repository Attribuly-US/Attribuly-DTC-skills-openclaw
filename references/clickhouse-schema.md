---
name: clickhouse-schema
version: 1.1.0
description: Declares the ClickHouse analysis tables currently approved for customer read-only access. OpenClaw reads this file to construct all SQL queries.
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

```yaml
- table: v4_events
  role: events
  description: Unified all-channel event fact table used for event, funnel, source, and order behavior analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: id
      type: String
      description: Unique event ID.
      sorting_key: true
    - name: sender
      type: String
      description: Event producer or sender.
    - name: other_event_id
      type: String
      description: Third-party event ID.
    - name: user_id
      type: String
      description: Unified user ID.
    - name: device_id
      type: String
      description: Device ID.
    - name: causality
      type: String
      description: Event causality marker such as cause or effect.
    - name: campaign
      type: String
      description: Event campaign name or label.
    - name: platform
      type: String
      description: Event platform.
    - name: source
      type: String
      description: Event source.
    - name: medium
      type: String
      description: Event medium.
    - name: channel
      type: String
      description: Event channel.
    - name: default_channel
      type: String
      description: Normalized default channel.
    - name: utm_campaign
      type: String
      description: UTM campaign value.
    - name: utm_source
      type: String
      description: UTM source value.
    - name: utm_medium
      type: String
      description: UTM medium value.
    - name: utm_platform
      type: String
      description: UTM platform value.
    - name: utm_content
      type: String
      description: UTM content value.
    - name: conversion_value
      type: Decimal64(2)
      description: Event conversion value.
    - name: sort_key
      type: UInt8
      description: Ordering key for same-timestamp events.
    - name: created_datetime
      type: DateTime64(3)
      description: Event timestamp.
      partition_key: true
      sorting_key: true
    - name: page_url
      type: String
      description: Page URL.
    - name: page_url_path
      type: String
      description: Page path.
    - name: page_referrer_domain
      type: String
      description: Referrer domain.
    - name: ad_account_id
      type: String
      description: Ad account ID attached to the event.
    - name: ad_campaign_id
      type: String
      description: Ad campaign ID attached to the event.
    - name: ad_set_id
      type: String
      description: Ad set ID attached to the event.
    - name: ad_id
      type: String
      description: Ad ID attached to the event.
    - name: impact_click_id
      type: String
      description: Impact click ID.
    - name: impact_campaign_id
      type: String
      description: Impact campaign ID.
    - name: share_a_sale_click_id
      type: String
      description: ShareASale click ID.
    - name: share_a_sale_campaign_id
      type: String
      description: ShareASale campaign ID.
    - name: awin_campaign_id
      type: String
      description: Awin campaign ID.
    - name: influencer_id
      type: String
      description: Influencer ID.
    - name: klaviyo_profile_id
      type: String
      description: Klaviyo profile ID.
    - name: receiver_email
      type: String
      description: Receiver email on email events.
    - name: organic_keyword
      type: String
      description: Organic search keyword.
    - name: search_keyword
      type: String
      description: On-site search keyword.
    - name: form_email
      type: String
      description: Form email captured in the event.
    - name: form_contact_id
      type: String
      description: Form contact ID.
    - name: form_tag
      type: String
      description: Form tag.
    - name: shop_sale
      type: Decimal64(2)
      description: Order sale amount including discount, duty, shipping, and tax.
    - name: shop_tax
      type: Decimal64(2)
      description: Order tax amount.
    - name: shop_shipping
      type: Decimal64(2)
      description: Order shipping amount.
    - name: shop_quantity
      type: UInt16
      description: Order item quantity.
    - name: checkout_order_id
      type: String
      description: Order ID associated with the event.
    - name: checkout_order_no
      type: String
      description: Order number associated with the event.
    - name: checkout_order_name
      type: String
      description: Order name associated with the event.
    - name: checkout_is_new_order
      type: UInt8
      description: Whether the order is a new order.
    - name: checkout_discount_codes
      type: Array(String)
      description: Discount codes applied to the order.

- table: v4_event_users
  role: events
  description: Stable user resolution table used for user-level deduplication and journey analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: id
      type: String
      description: Stable user ID.
      sorting_key: true
    - name: created_datetime
      type: DateTime64(3)
      description: User creation timestamp.
      partition_key: true
      sorting_key: true
    - name: updated_datetime
      type: DateTime64(3)
      description: User update timestamp.
    - name: device_id
      type: String
      description: Device ID.
    - name: temporary_contact_id
      type: String
      description: Temporary contact ID.
    - name: contact_id
      type: String
      description: CRM contact ID.

- table: v4_event_paths
  role: attribution
  description: Event-level attribution path table that connects each effect event or order to historical cause touchpoints.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: user
      type: String
      description: User owning the path.
    - name: effect_id
      type: String
      description: Effect event ID.
      sorting_key: true
    - name: effect_channel
      type: String
      description: Effect event channel.
    - name: effect_campaign
      type: String
      description: Effect event campaign.
    - name: effect_platform
      type: String
      description: Effect event platform.
    - name: effect_source
      type: String
      description: Effect event source.
    - name: effect_medium
      type: String
      description: Effect event medium.
    - name: effect_conversion_value
      type: Decimal64(2)
      description: Effect event conversion value.
    - name: effect_created_datetime
      type: DateTime64(3)
      description: Effect event timestamp.
      partition_key: true
      sorting_key: true
    - name: effect_checkout_order_id
      type: String
      description: Order ID attached to the effect event.
    - name: effect_checkout_order_no
      type: String
      description: Order number attached to the effect event.
    - name: effect_checkout_order_name
      type: String
      description: Order name attached to the effect event.
    - name: effect_shop_sale
      type: Decimal64(2)
      description: Total order sale amount on the effect event.
    - name: effect_shop_quantity
      type: UInt16
      description: Total item quantity on the effect event.
    - name: effect_shop_items_quantity
      type: Array(UInt16)
      description: Per-item quantity array aligned with product arrays.
    - name: effect_shop_items_sale
      type: Array(Decimal64(2))
      description: Per-item sale amount array aligned with product arrays.
    - name: effect_shop_items_variants_name
      type: Array(String)
      description: Per-item variant name array aligned with product arrays.
    - name: effect_shop_items_skus
      type: Map(String, UInt16)
      description: SKU-to-array-position mapping for order items.
    - name: effect_shop_items_products
      type: Array(String)
      description: Ordered product ID array for the converted order.
    - name: effect_shop_items_products_name
      type: Array(String)
      description: Ordered product name array for the converted order.
    - name: cause_id
      type: String
      description: Historical cause touchpoint event ID.
      sorting_key: true
    - name: cause_channel
      type: String
      description: Cause touchpoint channel.
    - name: cause_default_channel
      type: String
      description: Normalized cause touchpoint channel.
    - name: cause_campaign
      type: String
      description: Cause touchpoint campaign.
    - name: cause_platform
      type: String
      description: Cause touchpoint platform.
    - name: cause_source
      type: String
      description: Cause touchpoint source.
    - name: cause_medium
      type: String
      description: Cause touchpoint medium.
    - name: cause_utm_campaign
      type: String
      description: Cause touchpoint UTM campaign.
    - name: cause_utm_source
      type: String
      description: Cause touchpoint UTM source.
    - name: cause_utm_medium
      type: String
      description: Cause touchpoint UTM medium.
    - name: cause_utm_platform
      type: String
      description: Cause touchpoint UTM platform.
    - name: cause_created_datetime
      type: DateTime64(3)
      description: Cause touchpoint timestamp.
    - name: cause_page_url
      type: String
      description: Cause touchpoint page URL.
    - name: cause_page_referrer_domain
      type: String
      description: Cause touchpoint referrer domain.
    - name: cause_ad_account_id
      type: String
      description: Cause touchpoint ad account ID.
    - name: cause_ad_campaign_id
      type: String
      description: Cause touchpoint ad campaign ID.
    - name: cause_ad_set_id
      type: String
      description: Cause touchpoint ad set ID.
    - name: cause_ad_id
      type: String
      description: Cause touchpoint ad ID.
    - name: cause_impact_click_id
      type: String
      description: Cause touchpoint Impact click ID.
    - name: cause_share_a_sale_click_id
      type: String
      description: Cause touchpoint ShareASale click ID.
    - name: cause_awin_campaign_id
      type: String
      description: Cause touchpoint Awin campaign ID.
    - name: conversions_all_first
      type: Int16
      description: First-touch all-channel attribution contribution.
    - name: conversions_all_last
      type: Int16
      description: Last-touch all-channel attribution contribution.
    - name: conversions_all_linear
      type: Int16
      description: Linear all-channel attribution contribution.
    - name: conversions_all_position
      type: Int16
      description: Position-based all-channel attribution contribution.
    - name: conversions_ad_full_credit
      type: Int16
      description: Full-credit ad attribution contribution.

- table: v4_link_paths
  role: attribution
  description: Link-level attribution path table for path analysis at URL or content-entry granularity.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: user
      type: String
      description: User owning the path.
    - name: effect_id
      type: String
      description: Effect event ID.
      sorting_key: true
    - name: effect_created_datetime
      type: DateTime64(3)
      description: Effect event timestamp.
      partition_key: true
      sorting_key: true
    - name: effect_page_url
      type: String
      description: Effect page URL.
    - name: effect_channel
      type: String
      description: Effect event channel.
    - name: effect_checkout_order_id
      type: String
      description: Order ID attached to the effect event.
    - name: cause_id
      type: String
      description: Cause event ID.
      sorting_key: true
    - name: cause_created_datetime
      type: DateTime64(3)
      description: Cause touchpoint timestamp.
    - name: cause_channel
      type: String
      description: Cause touchpoint channel.
    - name: cause_campaign
      type: String
      description: Cause touchpoint campaign.
    - name: cause_source
      type: String
      description: Cause touchpoint source.
    - name: cause_medium
      type: String
      description: Cause touchpoint medium.
    - name: cause_page_url
      type: String
      description: Cause touchpoint page URL.

- table: v4_ad_date_insights
  role: ad_spend
  description: Daily ad performance fact table for campaign, ad set, and ad-level spend analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: account_id
      type: String
      description: Ad account ID.
      sorting_key: true
    - name: campaign_id
      type: String
      description: Ad campaign ID.
      sorting_key: true
    - name: set_id
      type: String
      description: Ad set ID.
      sorting_key: true
    - name: ad_id
      type: String
      description: Ad ID.
      sorting_key: true
    - name: created_datetime
      type: DateTime64(3)
      description: Insight date timestamp.
      partition_key: true
      sorting_key: true
    - name: platform
      type: String
      description: Ad platform.
    - name: impressions
      type: Decimal64(2)
      description: Impressions.
    - name: clicks
      type: Decimal64(2)
      description: Clicks.
    - name: cpm
      type: Decimal64(2)
      description: Cost per mille.
    - name: cpc
      type: Decimal64(2)
      description: Cost per click.
    - name: cost
      type: Decimal64(2)
      description: Spend amount.
    - name: conversion_value
      type: Decimal64(2)
      description: Conversion value.
    - name: frequency
      type: Decimal64(2)
      description: Frequency.
    - name: outbound_clicks
      type: Decimal64(2)
      description: Outbound clicks.
    - name: purchase_1d_view
      type: Decimal64(2)
      description: One-day view purchase count.
    - name: purchase_conversion_1d_view
      type: Decimal64(2)
      description: One-day view purchase conversion value.

- table: v4_products
  role: other
  description: Product dimension table used for product-level analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: id
      type: String
      description: Product ID.
      sorting_key: true
    - name: name
      type: String
      description: Product name.
    - name: status
      type: String
      description: Product status.
    - name: created_datetime
      type: DateTime64(3)
      description: Product creation timestamp.
      sorting_key: true
    - name: published_datetime
      type: DateTime64(3)
      description: Product publish timestamp.

- table: v4_shopify_customer_tags
  role: other
  description: Customer tag dimension table used for segmentation and exclusion logic.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: contact_id
      type: String
      description: CRM contact ID.
    - name: customer_id
      type: String
      description: Shopify customer ID.
      sorting_key: true
    - name: tag
      type: String
      description: Customer tag.
      sorting_key: true

- table: v4_shopify_order_tags
  role: other
  description: Order tag dimension table used for order segmentation and exclusion logic.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: order_id
      type: String
      description: Shopify order ID.
      sorting_key: true
    - name: order_name
      type: String
      description: Shopify order name.
    - name: tag
      type: String
      description: Order tag.
      sorting_key: true

- table: v4_ad_impact_action
  role: attribution
  description: Impact affiliate action table used for commission and order attribution analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: ad_account_id
      type: String
      description: Ad account ID.
    - name: action_id
      type: String
      description: Impact action ID.
      sorting_key: true
    - name: campaign_id
      type: String
      description: Campaign ID.
    - name: ad_id
      type: String
      description: Ad ID.
    - name: pay_out
      type: Decimal64(2)
      description: Commission payout.
    - name: order_name
      type: String
      description: Order name.
    - name: event_date
      type: DateTime64(3)
      description: Action timestamp.
      partition_key: true
      sorting_key: true

- table: v4_ad_impact_click
  role: attribution
  description: Impact affiliate click table used for click-to-order attribution analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: click_id
      type: String
      description: Impact click ID.
      sorting_key: true
    - name: campaign_id
      type: String
      description: Campaign ID.
    - name: campaign_name
      type: String
      description: Campaign name.
    - name: ad_id
      type: String
      description: Ad ID.
    - name: ad_name
      type: String
      description: Ad name.
    - name: event_date
      type: DateTime64(3)
      description: Click timestamp.
      partition_key: true
      sorting_key: true

- table: v4_ad_share_a_sale_action
  role: attribution
  description: ShareASale affiliate action table used for commission and order attribution analysis.
  engine: VersionedCollapsingMergeTree
  columns:
    - name: ad_account_id
      type: String
      description: Ad account ID.
    - name: action_id
      type: String
      description: ShareASale action ID.
      sorting_key: true
    - name: campaign_id
      type: String
      description: Campaign ID.
    - name: ad_id
      type: String
      description: Ad ID.
    - name: pay_out
      type: Decimal64(2)
      description: Commission payout.
    - name: order_name
      type: String
      description: Order name.
    - name: event_date
      type: DateTime64(3)
      description: Action timestamp.
      partition_key: true
      sorting_key: true

- table: v4_impression_events
  role: attribution
  description: Impression attribution support table used for predicted exposure-touch relationships.
  engine: ReplacingMergeTree
  columns:
    - name: id
      type: String
      description: Impression event ID.
    - name: first_event_id
      type: String
      description: First event ID for the user path.
    - name: user_id
      type: String
      description: User ID.
    - name: device_id
      type: String
      description: Device ID.
    - name: channel
      type: String
      description: Impression channel.
    - name: default_channel
      type: String
      description: Normalized impression channel.
    - name: source
      type: String
      description: Impression source.
    - name: platform
      type: String
      description: Impression platform.
    - name: campaign_id
      type: String
      description: Campaign ID.
    - name: account_id
      type: String
      description: Account ID.
    - name: impression_ratio
      type: Float64
      description: Predicted impression attribution ratio.
    - name: sort_key
      type: Int8
      description: Ordering key for same-timestamp events.
    - name: created_datetime
      type: DateTime64(3)
      description: Impression event timestamp.
      partition_key: true
    - name: effect_event_id
      type: String
      description: Effect event ID linked to the impression.
      sorting_key: true
    - name: effect_order_id
      type: String
      description: Order ID linked to the impression.
    - name: created_at
      type: DateTime64(3)
      description: Record creation timestamp.
```

---

## Query Constraints

> OpenClaw MUST follow these rules when building queries against this schema:

- Only use tables and columns declared above. Never guess or add columns not listed here.
- Always filter on the column marked `partition_key: true` in every query — this is required for ClickHouse partition pruning. Without it, queries will do a full table scan.
- Use `enum_values` to know the valid values for event type / status filtering. Never assume event names not listed here.
- Never access `system.*` tables — the account does not have permission.
- For columns of type `Nullable(T)`, wrap in `ifNull(col, default)` or check `IS NOT NULL` before aggregating.
- If the user asks for a metric that requires a column not present in this schema, respond: _"That field is not available in the current schema. Please check if it needs to be added to the allowed table list."_
