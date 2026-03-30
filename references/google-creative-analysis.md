---
name: google-creative-analysis
version: 1.0.0
description: Extracts, processes, and analyzes creative performance data for Google Ads. Identifies creative fatigue, low CTRs, and poor conversion drivers by combining Google's platform metrics with Attribuly's deduplicated backend data.
---
# Skill: AllyClaw Google Creative Analysis

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly Google Creative Analysis is superior:**

| Capability | Google Ads Native | Attribuly | Why It Matters |
|------------|-------------------|-----------|----------------|
| **True ROAS** | Inflated by self-attribution | ✅ Deduplicated linear attribution | Shows the actual business impact of specific creatives. |
| **New Customer Value** | All conversions look the same | ✅ Tracks `new_order_roas` by ad | Identifies which creatives acquire net-new customers. |
| **Profitability** | Revenue based only | ✅ Includes `profit` and `margin` | Prevents scaling creatives that lose money. |

**Key Insight:** Google often auto-optimizes towards the highest CTR or immediate conversion, even if it cannibalizes organic traffic or generates unprofitable sales. This skill finds the creatives that actually drive profitable, net-new business.

---

## When to Trigger This Skill

### Automatic Triggers
- When Google Ads CTR drops > 15% week-over-week.
- When Google Ads CPA increases > 20% week-over-week.
- Triggered as a secondary diagnostic skill by `google_ads_performance` or `weekly_marketing_performance`.

### Manual Triggers (User Commands)
- "Which Google ads are performing best?"
- "Analyze our Google creative performance."
- "Are we experiencing creative fatigue on Google?"
- "Find the worst performing search ads."

### Context Triggers
- After launching new creative assets (images, videos, or copy).
- When overall account performance is declining despite stable budgets.

---

## Skill Purpose

Provide a deep-dive diagnostic report on Google Ads creative performance:
1. **Performance Ranking** — Rank creatives (ads/assets) by true ROAS and Profit.
2. **Fatigue Detection** — Identify high-spend, declining-CTR creatives that need rotation.
3. **Acquisition Quality** — Highlight creatives driving net-new customers vs. repeat buyers.
4. **Actionable Recommendations** — Provide specific instructions to pause, scale, or iterate creatives.

---

## Data Sources

### Primary APIs

#### 1. Get Ad Analysis (Campaign/Ad Set/Ad Level)
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Fetch granular, ad-level performance metrics, combining platform data (impressions, clicks) with Attribuly's deduplicated conversion data.

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | string | Yes | Start date (`YYYY-MM-DD`) |
| `end_date` | string | Yes | End date (`YYYY-MM-DD`) |
| `dimensions` | array[string] | Yes | `["channel", "campaign", "ad_set", "ad_name"]` |
| `filters` | array[object] | Yes | Filter for `channel = 'google'` |
| `model` | string | Yes | Attribution model (e.g., `linear`) |

**Primary Fields Used**

| Field | Description |
|-------|-------------|
| `ad_name` | Name/Identifier of the creative |
| `impressions` | Platform impressions |
| `clicks` | Platform clicks |
| `ctr` | Click-through rate |
| `spend` | Ad spend |
| `conversions` | Deduplicated purchases |
| `roas` | Deduplicated Return on Ad Spend |
| `new_order_roas` | ROAS from new customers |
| `profit` | Calculated profit (Revenue - Spend - COGS) |

#### 2. Google Ads Query API (Supplemental Data)
**Endpoint:** `POST /{version}/api/source/google-query`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Fetch Google-native qualitative data like Quality Score components, search term relevancy, and PMax asset performance via GAQL queries.

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `account_id` | string | Yes | Google Ads customer ID (obtain from Connected Sources API) |
| `gaql` | string | Yes | Google Ads Query Language query string |

**Key GAQL Resources Used:**
- `keyword_view`: Extracts Quality Score, Ad Relevance (`creative_quality_score`), and Expected CTR.
- `asset_group_asset`: Evaluates Performance Max asset labels (`BEST`, `GOOD`, `LOW`).
- `search_term_view`: Identifies wasted spend on irrelevant queries.

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `version` | `v2-4-2` | API version |
| `start_date` | Today - 14 days | Standard creative evaluation window |
| `end_date` | Today - 1 day | Yesterday, explicitly excluding today |
| `model` | `linear` | Linear attribution |
| `page_size` | `100` | Max records for deep analysis |

---

## Execution Steps

### Step 1: Validate Input
- Ensure `start_date <= end_date`.
- Ensure integration with Google Ads is active via the Settings API if necessary.

### Step 2: Fetch Ad-Level Data
- Call `/api/get/ad-analysis/list` with dimensions `["channel", "campaign", "ad_set", "ad_name"]`.
- Filter strictly for `channel = 'google'`.

### Step 3: Fetch Supplemental Google Data
- First, retrieve the connected Google Ads account ID via `POST /{version}/api/get/connection/source` with `platform_type: "google"`.
- Execute GAQL queries via `POST /{version}/api/source/google-query` to pull:
  - Quality Score metrics (`creative_quality_score`) from `keyword_view` to assess ad relevance.
  - PMax asset performance (`asset_group_asset.performance_label`) from `asset_group_asset`.
  - Search term data from `search_term_view` to identify wasted spend.

### Step 4: Implement Caching (If Applicable)
- Cache the response for 1 hour to prevent rate-limiting on repeated granular queries during the same session.

### Step 5: Analyze and Categorize Creatives
Sort and categorize the `records` into distinct buckets:
- **Top Performers (Scale):** High Spend, ROAS > Target, Profit > 0, Quality Score > 7.
- **Fatigued (Refresh):** High Spend, declining CTR vs historical average, declining ROAS.
- **Bleeders (Pause):** High Spend, Profit < 0, Low `new_order_roas`, Low Ad Relevance.
- **Hidden Gems (Test):** Low Spend, High CTR, High `new_order_roas`.

### Step 6: Generate Recommendations & A/B Testing Protocols
- Formulate specific actions for the categorized creatives.
- If recommending a new test, enforce A/B testing protocols: isolate one variable, ensure minimum 10,000 impressions / 50 conversions, and run for at least 7 days before declaring a winner based on True ROAS.

---

## Key Metrics to Analyze

| Metric | Field Name | Why It Matters |
|--------|------------|----------------|
| **Engagement** | `ctr` | Indicates initial creative resonance and ad relevance. Benchmark: > 3% for Search, > 1.5% for Display. |
| **Ad Relevance** | `creative_quality_score` | Google's measure of how well the ad matches the intent. Target: ABOVE_AVERAGE. |
| **Efficiency** | `roas`, `cpa` | Determines if the creative converts profitably. |
| **Scale** | `spend`, `impressions` | Shows how much the platform trusts the creative. |
| **Opportunity** | `search_impression_share` | Identifies if a top-performing creative is limited by budget or rank. |
| **Incrementality** | `new_order_roas` | Proves the creative is generating net-new business. |
| **Bottom Line** | `profit` | The ultimate measure of creative success. |

---

## Standardized Creative Evaluation Rubric

When qualitative review of an ad is required, evaluate it using this 1-5 point rubric:
- **Visual Hierarchy:** (1) Cluttered/Illegible → (5) Single clear focal point.
- **Messaging Clarity:** (1) Vague/Jargon → (5) Clear value proposition addressing pain points.
- **Brand Alignment:** (1) Inconsistent tone → (5) Instantly recognizable brand voice.
- **CTA Strength:** (1) Passive (e.g., "Submit") → (5) Action-oriented with urgency (e.g., "Get 20% Off Today").

---

## Root Cause Analysis Logic

### Scenario 1: High CTR, Low ROAS
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `ctr` > 3%, `roas` < 1.0 | Clickbait ad copy, irrelevant landing page, or poor pricing fit. | Review the landing page experience. Ensure the ad promise matches the LP. |

### Scenario 2: Low CTR, High ROAS
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `ctr` < 1%, `roas` > 3.0 | Highly qualified but narrow audience. Creative is filtering out bad clicks. | Do not pause. Try iterating variations of this creative to slightly broaden appeal without losing quality. |

### Scenario 3: Creative Fatigue
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| High `impressions`, `ctr` dropping over time | Audience exhaustion. | Pause and replace with fresh visual assets or updated copy hooks. |

---

## Expected Output (Consolidated Dashboard)

```markdown
# 🎨 Google Creative Analysis Dashboard
Date Range: [start] to [end]

## 1) Executive Overview
- **Total Google Ad Spend Analyzed:** $[X]
- **Blended ROAS:** [X.Xx]
- **Primary Insight:** [e.g., Video assets in Campaign Y are driving 80% of new customer revenue, while static images are bleeding profit.]

## 2) The Winners Circle (Scale List)
| Ad Name | Spend | CTR | Quality Score | True ROAS | ncROAS | Profit |
|---------|------:|----:|--------------:|----------:|-------:|-------:|
| Ad_Copy_A | $500 | 4.2% | 8/10 | 3.5x | 2.8x | +$1,200 |
*Action: Scale budget slowly (15-20% every 2-3 days).*

## 3) The Fatigue Warning (Refresh List)
| Ad Name | Spend | CTR | Historical CTR | True ROAS | Profit |
|---------|------:|----:|---------------:|----------:|-------:|
| Ad_Copy_C | $600 | 1.5% | 3.2% (Peak) | 1.8x | +$100 🟡 |
*Action: Rotate creative or pause.*

## 4) The Bleeders (Pause List)
| Ad Name | Spend | CTR | True ROAS | ncROAS | Profit |
|---------|------:|----:|----------:|-------:|-------:|
| Ad_Copy_B | $800 | 0.8% | 0.8x | 0.2x | -$400 🔴 |
*Action: Immediate pause to stop profit bleed.*

## 5) Strategic Recommendations & DTC Best Practices
1. **Competitive Gap:** Test an "Us vs. Them" angle as it is currently missing from the active ad rotation.
2. **Seasonal Trend:** Pre-load Q4 creatives 3-4 weeks early to exit the learning phase before peak CPAs hit.
3. **Audience Strategy:** Move `Ad_Copy_A` strictly to Prospecting (Top of Funnel) due to its high `ncROAS`.
```

---

## Error Handling & Data Validation

- **Rate Limit (`429`)**: Apply exponential backoff (1s, 2s, 4s).
- **Empty Results**: If `data.records.length === 0`, output: "No Google Ads creative data found for the selected date range. Verify active campaigns and date selection."
- **Missing Dimensions**: Ensure `ad_name` is present. If missing, fall back to analyzing at the `ad_set` level and flag the missing data.
- **Data Validation**: Ensure `spend` > 0 before calculating efficiency ratios to prevent division by zero.

---

## Thresholds for Alerts

| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| `ctr` | < 1.5% | < 0.8% |
| `roas` | < Target ROAS | < 1.0 (Losing money) |
| `profit` | < $50 | < $0 |

---

## Example API Calls

### Fetch Granular Ad Performance for Google
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-14",
    "dimensions": ["channel", "campaign", "ad_set", "ad_name"],
    "filters": [
      {
        "key": "channel",
        "operator": "eq",
        "value": "google"
      }
    ],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 100
  }'
```

### Fetch Search Terms Data
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT search_term_view.search_term, search_term_view.status, campaign.name, ad_group.name, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions, metrics.ctr FROM search_term_view WHERE segments.date BETWEEN '\''2026-03-01'\'' AND '\''2026-03-14'\'' AND metrics.impressions > 0 ORDER BY metrics.cost_micros DESC LIMIT 100"
  }'
```

### Fetch Quality Score Data
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT ad_group_criterion.keyword.text, ad_group_criterion.quality_info.quality_score, ad_group_criterion.quality_info.creative_quality_score, ad_group_criterion.quality_info.post_click_quality_score, ad_group_criterion.quality_info.search_predicted_ctr, campaign.name, ad_group.name, metrics.impressions, metrics.clicks, metrics.cost_micros FROM keyword_view WHERE segments.date BETWEEN '\''2026-03-01'\'' AND '\''2026-03-14'\'' AND ad_group_criterion.status = '\''ENABLED'\'' AND metrics.impressions > 0 ORDER BY metrics.cost_micros DESC LIMIT 100"
  }'
```

### Fetch PMax Asset Performance
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT asset_group.name, asset_group.id, asset_group_asset.asset, asset_group_asset.field_type, asset_group_asset.performance_label, asset_group_asset.status, campaign.name FROM asset_group_asset WHERE campaign.advertising_channel_type = '\''PERFORMANCE_MAX'\'' AND asset_group_asset.status = '\''ENABLED'\''"
  }'
```

---

## Dependencies

| Dependency | Type | Purpose |
|------------|------|---------|
| Attribuly Data API | External API | Fetch granular ad-level metrics |
| Attribuly Google Query API | External API | Execute GAQL queries for Quality Score, search terms, and PMax assets |
| Caching Module | System | Temporarily store high-volume ad data to optimize performance |

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `google_ads_performance` | Parent skill; triggers creative analysis when campaign efficiency drops. |
| `creative_fatigue_detector` | Triggers automatically when specific frequency/CTR thresholds are crossed across multiple platforms. |
| `landing_page_analysis` | Triggered when high CTR creatives result in low ROAS (indicating a post-click drop-off). |
