---
name: google-ads-performance
version: 1.1.0
description: Diagnose Google Ads and PMax campaigns for your ecommerce store. Uncover true ncROAS and stop brand cannibalization with Attribuly.
---
# Skill: AllyClaw Google Ads Analyzer for DTC

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly data is superior to Google Ads Manager:**

| Metric | Google Ads Manager | Attribuly | Why It Matters |
|--------|-------------------|-----------|----------------|
| **ncROAS** | ❌ Not available | ✅ `new_order_roas` | Measures TRUE incremental value from new customers |
| **ncPurchase** | ❌ Not available | ✅ `new_lead_conversions` | Counts only first-time buyers, not repeat purchases |
| **Profit** | ❌ Not available | ✅ `profit` | Real profit after COGS, not just revenue |
| **Margin** | ❌ Not available | ✅ `margin` | Profit margin % for profitability analysis |
| **LTV** | ❌ Not available | ✅ `ltv` | Customer lifetime value for long-term ROI |
| **True ROAS** | ⚠️ Over-attributed | ✅ `roas` | First-party data, not inflated by view-through |

**Key Insight:** Google Ads Manager inflates ROAS by 30-50% due to view-through attribution and brand cannibalization. Attribuly shows the TRUE incremental impact.

---

## When to Trigger This Skill

### Automatic Triggers
- When `weekly_marketing_performance` detects Google Ads ROAS dropped >15%
- When `weekly_marketing_performance` detects Google Ads CPA increased >15%
- When Google Ads spend is >30% of total ad spend and performance shifts

### Manual Triggers (User Commands)
- "How is Google Ads doing?"
- "Google Ads performance"
- "Analyze Google campaigns"
- "Google Ads report"
- "What's happening with our Google spend?"
- "Google Search performance"
- "PMax performance"

### Context Triggers
- When user asks about search campaigns
- When user mentions branded vs non-branded performance
- When user asks about Shopping or PMax campaigns

---

## Skill Purpose

Provide a deep-dive analysis of Google Ads performance at **Campaign**, **Ad Set** (Ad Group), and **Ad** levels. Compare current period vs. previous period, identify top performers and underperformers, and provide actionable recommendations.

---

## Data Sources

### Primary API
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Get Google Ads attribution data at multiple levels.

### API Calls Required (4 calls per period, 8 total for comparison)

#### 1. Channel Level (Google Overview)
```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimension": "channel",
  "model": "linear",
  "goal": "purchase",
  "page": 1,
  "page_size": 100
}
```
*Filter results where `channel` = "google" or "google_ads"*

#### 2. Campaign Level
```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimension": "campaign",
  "model": "linear",
  "goal": "purchase",
  "page": 1,
  "page_size": 100
}
```
*Filter results where `channel` = "google" or "google_ads"*

#### 3. Ad Set Level (Ad Group)
```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimension": "ad_set",
  "model": "linear",
  "goal": "purchase",
  "page": 1,
  "page_size": 100
}
```
*Filter results where `channel` = "google" or "google_ads"*

#### 4. Ad Level
```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimension": "ad",
  "model": "linear",
  "goal": "purchase",
  "page": 1,
  "page_size": 100
}
```
*Filter results where `channel` = "google" or "google_ads"*

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Linear attribution |
| `goal` | `purchase` | Purchase conversions |
| `page_size` | `100` | Get all records |
| `channel_filter` | `google`, `google_ads` | Filter for Google only |

---

## Available Metrics from API

### 🌟 Attribuly-Exclusive Metrics (NOT in Google Ads Manager)

| Metric | Field Name | Description | Why It Matters |
|--------|------------|-------------|----------------|
| **ncROAS** | `new_order_roas` | New Customer ROAS = new_order_conversion_value / spend | **TRUE incrementality** - excludes repeat buyers |
| **ncRevenue** | `new_order_conversion_value` | Revenue from first-time buyers only | Shows **incremental revenue** from ads |
| **ncConversions** | `new_order_conversions` | New customer purchase count | Measures **real acquisition**, not repeat orders |
| **Profit** | `profit` | Net profit = Revenue - COGS - Spend | **Real profitability**, not vanity ROAS |
| **Margin** | `margin` | Profit margin % = profit / revenue | Identifies **profitable vs unprofitable** campaigns |
| **LTV** | `ltv` | Customer Lifetime Value | Long-term ROI for **sustainable growth** |
| **True ROAS** | `roas` | Attribuly first-party attributed ROAS | **Accurate attribution** vs inflated platform ROAS |
| **Leads** | `new_lead_conversions` | Lead/email subscriber conversions | Track **top-of-funnel** acquisition |

### Standard Metrics

| Metric | Field Name | Description |
|--------|------------|-------------|
| **Channel** | `channel` | "google" or "google_ads" |
| **Campaign** | `campaign` | Campaign name |
| **Ad Set** | `ad_set` | Ad Group name |
| **Ad Name** | `ad_name` | Ad name |
| **Conversions** | `conversions` | Attribuly-attributed purchases |
| **Revenue** | `conversion_value` | Attribuly-attributed revenue |
| **Platform Revenue** | `ad_net_conversion_value` | Google-reported conversion value |
| **Platform ROAS** | `ad_net_roas` | Google-reported ROAS |
| **Spend** | `spend` | Ad spend |
| **CPA** | `cpa` | Cost Per Acquisition |
| **Impressions** | `impressions` | Total impressions |
| **CPM** | `cpm` | Cost per 1000 impressions |
| **Clicks** | `clicks` | Total clicks |
| **CVR** | `cvr` | Conversion Rate |
| **CPC** | `cpc` | Cost Per Click |
| **CTR** | `ctr` | Click-Through Rate |

---

## Execution Steps

### Step 1: Fetch Google Ads Data at All Levels
Make 4 API calls for **Current Period** (last 7 days):
1. Channel level (to get Google totals)
2. Campaign level
3. Ad Set level
4. Ad level

### Step 2: Fetch Comparison Data
Make 4 API calls for **Previous Period** (prior 7 days).

### Step 3: Filter for Google Only
Filter all results where `channel` contains "google".

### Step 4: Calculate WoW Changes
For each campaign/ad set/ad, calculate:
```
change_percent = ((current_value - previous_value) / previous_value) * 100
```

### Step 5: Segment Campaigns by Type
Categorize campaigns into:
- **Brand Search**: Campaign name contains "brand", "branded"
- **Non-Brand Search**: Campaign name contains "search", "generic", "non-brand"
- **Shopping**: Campaign name contains "shopping", "pla"
- **PMax**: Campaign name contains "pmax", "performance max"
- **Display**: Campaign name contains "display", "gdn"
- **YouTube**: Campaign name contains "youtube", "video"
- **Other**: Everything else

### Step 6: Identify Issues
Flag campaigns/ad sets/ads with:
- ROAS < Target ROAS
- CPA > Target CPA
- CTR < 1.5% (for Search)
- CVR < 2%
- Spend > $100 with 0 conversions

### Step 7: Generate Recommendations
Based on issues found, provide specific actions.

---

## Google Ads Specific Analysis

### 🎯 Attribuly-Powered Incrementality Analysis

#### The Incrementality Score (Attribuly Exclusive)
Calculate for each campaign:
```
Incrementality Score = ncROAS / ROAS × 100

Where:
- ncROAS = new_order_roas (revenue from NEW customers only)
- ROAS = roas (total attributed revenue)
```

| Incrementality Score | Interpretation | Action |
|---------------------|----------------|--------|
| >80% | ✅ Highly Incremental | Scale budget |
| 50-80% | 🟡 Moderate | Optimize targeting |
| 30-50% | 🟠 Low Incrementality | Review audience overlap |
| <30% | 🔴 Cannibalizing | Reduce budget, fix targeting |

#### Profitability Analysis (Attribuly Exclusive)
```
Profitable Campaign = margin > 0 AND profit > 0

True Profit ROAS = profit / spend
```

| Metric | Healthy | Warning | Critical |
|--------|---------|---------|----------|
| Margin | >20% | 10-20% | <10% |
| Profit | >$0 | Break-even | Negative |
| True Profit ROAS | >0.3 | 0.1-0.3 | <0.1 |

### Brand vs. Non-Brand Analysis (Enhanced with Attribuly Metrics)
| Metric | Brand | Non-Brand | Benchmark | Attribuly Insight |
|--------|-------|-----------|-----------|-------------------|
| ROAS | Usually high (>5x) | Target (2-3x) | Brand should be >4x | Compare to ncROAS |
| **ncROAS** | Should be LOW | Should be HIGH | Non-brand ncROAS is key | **TRUE growth metric** |
| CPA | Usually low | Target CPA | Non-brand CPA is key | Check ncCPA |
| **Profit** | High margin | Lower margin | Both should be profitable | **Real bottom line** |
| Impression Share | Target >90% | Varies | Protect brand terms | - |

**Key Insight (Attribuly Exclusive):** 
- If Brand ROAS is high but Brand **ncROAS is also high** → Brand is acquiring new customers (good)
- If Brand ROAS is high but Brand **ncROAS is low** → Brand is just capturing existing customers (not incremental)
- **Always prioritize ncROAS over ROAS for growth decisions**

### PMax Incrementality Analysis (Attribuly Exclusive)
PMax campaigns require special attention with Attribuly metrics:

| Check | Formula | Interpretation |
|-------|---------|----------------|
| **Incrementality Gap** | `(roas - ncROAS) / roas × 100` | >50% = cannibalizing existing customers |
| **Attribution Inflation** | `(ad_net_roas - roas) / ad_net_roas × 100` | >30% = Google over-attributing |
| **Profit Check** | `profit > 0` | Negative = losing money despite "good ROAS" |
| **New Customer %** | `new_order_conversion_value / conversion_value × 100` | <50% = not driving acquisition |

**PMax Decision Matrix:**
| ncROAS | Profit | Action |
|--------|--------|--------|
| High | Positive | ✅ Scale aggressively |
| High | Negative | 🟡 Check COGS, optimize products |
| Low | Positive | 🟠 Reduce budget, improve audience signals |
| Low | Negative | 🔴 Pause or restructure |

### Shopping Analysis (Enhanced with Profit)
- Check product-level **profit** (not just ROAS)
- Identify products with high ROAS but **negative margin**
- Flag products with high spend but low **ncROAS** (repeat buyers only)
- Prioritize products with high **LTV** for scaling

---

## Root Cause Analysis Logic (Enhanced with Attribuly Metrics)

### Scenario 1: Overall Google ROAS Dropped
**Check in order:**
1. **Brand Search dropped?** → Competitor bidding or brand issue
2. **Non-Brand Search dropped?** → Keyword/bid issue or market change
3. **PMax dropped?** → Asset group fatigue or audience saturation
4. **Shopping dropped?** → Product feed or pricing issue

### Scenario 2: CPA Increased
**Check in order:**
1. **CPC increased?** → Competition or Quality Score issue
2. **CVR decreased?** → Landing page or offer issue
3. **CTR decreased?** → Ad copy fatigue or relevance issue

### Scenario 3: Spend Increased but Revenue Flat
**Check:**
1. Which campaign type increased spend?
2. Is it a new campaign in learning phase?
3. Is PMax cannibalizing brand search?

### Scenario 4: High Platform ROAS, Low Attribuly ROAS
**Diagnosis:** Google is over-attributing (likely brand/retargeting)
**Action:** Check if conversions are truly incremental using ncROAS.

### 🆕 Scenario 5: High ROAS but Low ncROAS (Attribuly Exclusive)
**Diagnosis:** Campaign is capturing existing customers, not acquiring new ones
**Root Cause Analysis:**
1. **Check ncROAS / ROAS ratio** — if <50%, campaign is cannibalizing
2. **Check audience overlap** — retargeting audiences may be too broad
3. **Check brand terms** — may be bidding on brand keywords unnecessarily

**Action:**
- Exclude existing customers from targeting
- Narrow audience to true prospecting
- Shift budget to campaigns with higher ncROAS

### 🆕 Scenario 6: Positive ROAS but Negative Profit (Attribuly Exclusive)
**Diagnosis:** Revenue looks good but COGS is eating margins
**Root Cause Analysis:**
1. **Check margin by campaign** — identify low-margin campaigns
2. **Check product mix** — are low-margin products being promoted?
3. **Check discount usage** — heavy discounting may hurt profit

**Action:**
- Exclude low-margin products from campaigns
- Adjust bidding to prioritize high-margin products
- Review promotional strategy

### 🆕 Scenario 7: Low LTV Customers from Ads (Attribuly Exclusive)
**Diagnosis:** Ads are acquiring low-quality customers who don't return
**Root Cause Analysis:**
1. **Compare LTV by campaign** — identify campaigns with low LTV
2. **Check discount dependency** — discount-driven customers have lower LTV
3. **Check product category** — some products attract one-time buyers

**Action:**
- Optimize for value-based bidding using LTV data
- Exclude heavy discount seekers
- Promote products with higher repeat purchase rates

---

## Output Format

```markdown
# Google Ads Performance Report
**Period:** [Current Start Date] to [Current End Date]
**Compared to:** [Previous Start Date] to [Previous End Date]
**Attribution Model:** Linear

---

## 📊 Google Ads Overview

| Metric | This Period | Last Period | Change | Status |
|--------|-------------|-------------|--------|--------|
| Revenue | $XX,XXX | $XX,XXX | +X.X% | ✅/🟡/🔴 |
| Conversions | XXX | XXX | +X.X% | ✅/🟡/🔴 |
| Spend | $X,XXX | $X,XXX | +X.X% | ✅/🟡/🔴 |
| ROAS (Attribuly) | X.XX | X.XX | +X.X% | ✅/🟡/🔴 |
| ROAS (Google) | X.XX | X.XX | +X.X% | ⚠️ Compare |
| CPA | $XX.XX | $XX.XX | +X.X% | ✅/🟡/🔴 |

### 🌟 Attribuly-Exclusive Insights

| Metric | This Period | Last Period | Change | Status |
|--------|-------------|-------------|--------|--------|
| **ncROAS** | X.XX | X.XX | +X.X% | ✅/🟡/🔴 |
| **ncRevenue** | $XX,XXX | $XX,XXX | +X.X% | ✅/🟡/🔴 |
| **Profit** | $X,XXX | $X,XXX | +X.X% | ✅/🟡/🔴 |
| **Margin** | XX.X% | XX.X% | +X.X% | ✅/🟡/🔴 |
| **Avg LTV** | $XXX | $XXX | +X.X% | ✅/🟡/🔴 |

**Attribution Gap:** Attribuly ROAS is X.X% [higher/lower] than Google-reported ROAS.
**Incrementality Score:** X.X% of revenue is from NEW customers (ncROAS/ROAS).
**Profitability:** [Profitable/Break-even/Unprofitable] with $X,XXX net profit.

---

## 📈 Performance by Campaign Type

| Type | Revenue | ROAS | ncROAS | Profit | Margin | Incrementality | Status |
|------|---------|------|--------|--------|--------|----------------|--------|
| Brand Search | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | XX% | ✅ |
| Non-Brand Search | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | XX% | 🟡 |
| Shopping | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | XX% | ✅ |
| PMax | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | XX% | 🔴 |

*Incrementality = ncROAS / ROAS × 100 (higher = more new customers)*

---

## 🏆 Top Performing Campaigns (by Profit - Attribuly Exclusive)

| Campaign | Type | Revenue | ROAS | ncROAS | Profit | Margin | LTV |
|----------|------|---------|------|--------|--------|--------|-----|
| [Campaign 1] | PMax | $X,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX |
| [Campaign 2] | Search | $X,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX |
| [Campaign 3] | Shopping | $X,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX |

*Ranked by Profit, not Revenue — shows TRUE business impact*

---

## ⚠️ Underperforming Campaigns (Attribuly Analysis)

| Campaign | Type | Spend | ROAS | ncROAS | Profit | Issue | Recommendation |
|----------|------|-------|------|--------|--------|-------|----------------|
| [Campaign A] | Search | $XXX | X.XX | X.XX | -$XXX | **Negative Profit** | Pause or optimize products |
| [Campaign B] | PMax | $XXX | X.XX | X.XX | $XXX | **Low ncROAS** | Cannibalizing - fix targeting |
| [Campaign C] | Shopping | $XXX | X.XX | X.XX | $XXX | **Low Margin** | Exclude low-margin products |

*Issues prioritized by Profit impact, not just ROAS*

---

## 🔍 Ad Group Analysis (Top Issues)

| Ad Group | Campaign | Spend | ROAS | CTR | Issue |
|----------|----------|-------|------|-----|-------|
| [Ad Group 1] | [Campaign] | $XXX | X.XX | X.X% | Low CTR |
| [Ad Group 2] | [Campaign] | $XXX | X.XX | X.X% | High CPA |

---

## 🎯 Ad-Level Insights

### Top Performing Ads
| Ad | Campaign | Clicks | CVR | ROAS |
|----|----------|--------|-----|------|
| [Ad 1] | [Campaign] | XXX | X.X% | X.XX |

### Underperforming Ads (Spend > $50, ROAS < 1)
| Ad | Campaign | Spend | Conversions | Action |
|----|----------|-------|-------------|--------|
| [Ad A] | [Campaign] | $XXX | 0 | Pause |

---

## 🚀 Actionable Recommendations

### High Priority
1. **[Action]**: [Specific recommendation]
   - *Campaign:* [Name]
   - *Why:* [Data-driven reasoning]
   - *Expected Impact:* [Projected outcome]

### Medium Priority
2. **[Action]**: [Specific recommendation]

### Monitor
3. **[Item to Watch]**: [What to keep an eye on]

---

## 📋 PMax Deep Dive (Attribuly Incrementality Analysis)

| Asset Group | Spend | ROAS | ncROAS | Profit | Margin | Incrementality | Status |
|-------------|-------|------|--------|--------|--------|----------------|--------|
| [Asset Group 1] | $XXX | X.XX | X.XX | -$XXX | -X% | XX% | 🔴 Cannibalizing + Unprofitable |
| [Asset Group 2] | $XXX | X.XX | X.XX | $XXX | XX% | XX% | ✅ Incremental + Profitable |

**PMax Incrementality Score:** X.X% of PMax revenue is from new customers.
**PMax Profitability:** $X,XXX total profit at XX% margin.
**PMax vs Brand Cannibalization:** X.X% overlap with brand search conversions.

### 🎯 PMax Optimization Recommendations (Attribuly-Powered)
1. **Scale** asset groups with ncROAS > X.XX AND profit > $0
2. **Pause** asset groups with ncROAS < X.XX OR profit < $0
3. **Optimize** asset groups with high ROAS but low ncROAS (add audience signals)
```

---

## Thresholds for Alerts

### Standard Metrics
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| ROAS Change | -10% to -20% | < -20% |
| CPA Change | +10% to +20% | > +20% |
| CTR (Search) | < 2% | < 1% |
| CVR | < 2% | < 1% |
| Spend with 0 conversions | > $100 | > $300 |

### 🌟 Attribuly-Exclusive Thresholds
| Metric | Warning (🟡) | Critical (🔴) | Why It Matters |
|--------|--------------|---------------|----------------|
| **ncROAS** | < 1.5 | < 1.0 | Not acquiring new customers profitably |
| **Incrementality (ncROAS/ROAS)** | < 50% | < 30% | Cannibalizing existing customers |
| **Profit** | < $0 (break-even) | < -$500 | Losing money despite "good ROAS" |
| **Margin** | < 15% | < 10% | Low profitability, unsustainable |
| **LTV** | < $50 | < $30 | Acquiring low-quality customers |
| **Attribution Gap (Platform vs Attribuly)** | > 30% | > 50% | Platform severely over-attributing |

---

## Complete Data Retrieval Workflow

### Step 0: Discover Connected Google Ads Accounts
Before querying Google Ads data, retrieve the connected account ID.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/connection/source" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "platform_type": "google"
  }'
```

**Response:**
```json
{
  "code": 1,
  "message": "Service succeed",
  "data": {
    "records": [
      {
        "account_id": "6622546829",
        "name": "My Store - Google Ads",
        "platform_type": "google",
        "currency": "USD",
        "connected": 1
      }
    ]
  }
}
```

**Extract `account_id`** (e.g., `6622546829`) for use in subsequent queries.

---

## Enhanced Data APIs (Now Available)

### 1. Search Terms Report
**Purpose:** Identify actual search queries triggering your ads. Critical for finding wasted spend.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT search_term_view.search_term, search_term_view.status, campaign.name, campaign.id, ad_group.name, ad_group.id, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions, metrics.conversions_value, metrics.ctr, metrics.average_cpc FROM search_term_view WHERE segments.date BETWEEN '\''2025-03-10'\'' AND '\''2025-03-16'\'' AND metrics.impressions > 0 ORDER BY metrics.cost_micros DESC LIMIT 100"
  }'
```

**Key Fields:**
| Field | Description |
|-------|-------------|
| `searchTermView.searchTerm` | The actual search query |
| `searchTermView.status` | ADDED, EXCLUDED, NONE |
| `metrics.costMicros` | Cost in micros (÷ 1,000,000 for actual cost) |

**Important:** Google does NOT disclose ~50% of search terms (shown as "(other)").

### 2. Quality Score & Keyword Data
**Purpose:** Diagnose why CPC is high or ads aren't showing.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT ad_group_criterion.keyword.text, ad_group_criterion.keyword.match_type, ad_group_criterion.quality_info.quality_score, ad_group_criterion.quality_info.creative_quality_score, ad_group_criterion.quality_info.post_click_quality_score, ad_group_criterion.quality_info.search_predicted_ctr, campaign.name, ad_group.name, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions, metrics.average_cpc FROM keyword_view WHERE segments.date BETWEEN '\''2025-03-10'\'' AND '\''2025-03-16'\'' AND ad_group_criterion.status = '\''ENABLED'\'' AND metrics.impressions > 0 ORDER BY metrics.cost_micros DESC LIMIT 100"
  }'
```

**Quality Score Components:**
| Component | Values | Impact |
|-----------|--------|--------|
| Expected CTR | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE | Ad relevance |
| Ad Relevance | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE | Keyword-ad match |
| Landing Page Exp. | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE | Page quality |

### 3. Impression Share Metrics
**Purpose:** Identify lost opportunity due to budget or rank.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT campaign.name, campaign.id, metrics.search_impression_share, metrics.search_budget_lost_impression_share, metrics.search_rank_lost_impression_share, metrics.search_absolute_top_impression_share, metrics.search_top_impression_share, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions FROM campaign WHERE segments.date BETWEEN '\''2025-03-10'\'' AND '\''2025-03-16'\'' AND campaign.advertising_channel_type = '\''SEARCH'\'' AND metrics.impressions > 0 ORDER BY metrics.cost_micros DESC"
  }'
```

**Interpretation Guide:**
| Metric | Good | Warning | Critical |
|--------|------|---------|----------|
| Search IS | >80% | 50-80% | <50% |
| Budget Lost IS | <10% | 10-30% | >30% |
| Rank Lost IS | <20% | 20-40% | >40% |

### 4. Device Breakdown
**Purpose:** Optimize bids by device type.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT campaign.name, campaign.id, segments.device, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions, metrics.conversions_value, metrics.ctr, metrics.average_cpc FROM campaign WHERE segments.date BETWEEN '\''2025-03-10'\'' AND '\''2025-03-16'\'' AND metrics.impressions > 0 ORDER BY campaign.id, segments.device"
  }'
```

**Device Values:** `MOBILE`, `DESKTOP`, `TABLET`, `CONNECTED_TV`, `OTHER`

### 5. PMax Asset Performance
**Purpose:** Identify winning assets in Performance Max campaigns.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT asset_group.name, asset_group.id, asset_group_asset.asset, asset_group_asset.field_type, asset_group_asset.performance_label, asset_group_asset.status, campaign.name, campaign.id FROM asset_group_asset WHERE campaign.advertising_channel_type = '\''PERFORMANCE_MAX'\'' AND asset_group_asset.status = '\''ENABLED'\''"
  }'
```

**Performance Labels:** `PENDING`, `LOW`, `GOOD`, `BEST`

---

## Error Handling & Logging

### Validate API Response
```javascript
function validateGoogleQueryResponse(response) {
  console.log(JSON.stringify({
    timestamp: new Date().toISOString(),
    level: 'DEBUG',
    skill: 'google_ads_performance',
    action: 'validate_response',
    response_code: response.code
  }));

  if (response.code !== 1) {
    console.error(JSON.stringify({
      timestamp: new Date().toISOString(),
      level: 'ERROR',
      skill: 'google_ads_performance',
      action: 'api_error',
      error: response.message
    }));
    return { success: false, error: response.message };
  }

  const record = response.data?.record?.[0];
  if (!record?.success) {
    console.error(JSON.stringify({
      timestamp: new Date().toISOString(),
      level: 'ERROR',
      skill: 'google_ads_performance',
      action: 'query_error',
      error: record?.error || 'Unknown error'
    }));
    return { success: false, error: record?.error };
  }

  console.log(JSON.stringify({
    timestamp: new Date().toISOString(),
    level: 'INFO',
    skill: 'google_ads_performance',
    action: 'query_success',
    result_count: record.results?.length || 0
  }));

  return { success: true, data: record.results };
}
```

### Rate Limiting
| API | Limit | Recommendation |
|-----|-------|----------------|
| Google Query API | 1,000 requests per 100 seconds per account | Batch queries, implement exponential backoff |
| Attribuly APIs | 100 requests per minute | Cache results, avoid redundant calls |

### Retry Strategy
```javascript
async function queryWithRetry(queryFn, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const result = await queryFn();
      if (result.success) return result;
      
      if (attempt < maxRetries - 1) {
        const delay = Math.pow(2, attempt) * 1000;
        console.log(`[RETRY] Attempt ${attempt + 1} failed, retrying in ${delay}ms...`);
        await new Promise(r => setTimeout(r, delay));
      }
    } catch (error) {
      if (attempt === maxRetries - 1) throw error;
    }
  }
}
```

---

## Standard Attribuly API Calls

### Get Google Campaign Performance (Current Period)
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-10",
    "end_date": "2025-03-16",
    "dimension": "campaign",
    "model": "linear",
    "goal": "purchase",
    "page": 1,
    "page_size": 100
  }'
```

### Get Google Ad Set (Ad Group) Performance
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-10",
    "end_date": "2025-03-16",
    "dimension": "ad_set",
    "model": "linear",
    "goal": "purchase",
    "page": 1,
    "page_size": 100
  }'
```

### Get Google Ad Level Performance
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-10",
    "end_date": "2025-03-16",
    "dimension": "ad",
    "model": "linear",
    "goal": "purchase",
    "page": 1,
    "page_size": 100
  }'
```

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `google_creative_analysis` | When CTR issues detected at ad level |
| `budget_optimization` | When budget reallocation needed |
| `funnel_analysis` | When CVR issues detected |
| `weekly_marketing_performance` | For cross-channel context |
