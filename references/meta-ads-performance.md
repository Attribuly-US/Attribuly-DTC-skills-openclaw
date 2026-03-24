---
name: meta-ads-performance
version: 1.1.0
description: Optimize Meta Ads for your DTC brand. Leverage Attribuly first-party data to bypass iOS14 tracking issues on WooCommerce and Shopify.
---
# Skill: AllyClaw Meta Ads Optimizer for Shopify

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly data is superior to Meta Ads Manager:**

| Metric | Meta Ads Manager | Attribuly | Why It Matters |
|--------|-----------------|-----------|----------------|
| **ncROAS** | ❌ Not available | ✅ `new_order_roas` | Measures TRUE incremental value from new customers |
| **ncPurchase** | ❌ Not available | ✅ `new_lead_conversions` | Counts only first-time buyers, not repeat purchases |
| **Profit** | ❌ Not available | ✅ `profit` | Real profit after COGS, not just revenue |
| **Margin** | ❌ Not available | ✅ `margin` | Profit margin % for profitability analysis |
| **LTV** | ❌ Not available | ✅ `ltv` | Customer lifetime value for long-term ROI |
| **True ROAS** | ⚠️ Inflated by view-through | ✅ `roas` | First-party data, not inflated by 1-day view |
| **Click-Based Attribution** | ⚠️ Mixed with view-through | ✅ Separated | See true click vs view impact |

**Key Insight:** Meta Ads Manager inflates ROAS by 40-60% due to 1-day view attribution. A "3.0 ROAS" in Meta often becomes "1.8 ROAS" in Attribuly — the TRUE incremental impact.

**The Prospecting Problem:** Meta reports high ROAS on retargeting, but Attribuly's ncROAS reveals these are mostly repeat customers. TRUE growth comes from prospecting with high ncROAS.

---

## When to Trigger This Skill

### Automatic Triggers
- When `weekly_marketing_performance` detects Meta Ads ROAS dropped >15%
- When `weekly_marketing_performance` detects Meta Ads CPA increased >15%
- When Meta Ads spend is >30% of total ad spend and performance shifts

### Manual Triggers (User Commands)
- "How is Meta Ads doing?"
- "Facebook Ads performance"
- "Meta performance"
- "Analyze Meta campaigns"
- "What's happening with our Facebook spend?"
- "Instagram Ads performance"
- "Advantage+ performance"
- "ASC performance"

### Context Triggers
- When user asks about prospecting vs retargeting
- When user mentions creative fatigue
- When user asks about Advantage+ Shopping Campaigns (ASC)
- When user asks about audience performance

---

## Skill Purpose

Provide a deep-dive analysis of Meta Ads (Facebook/Instagram) performance at **Campaign**, **Ad Set**, and **Ad** levels. Compare current period vs. previous period, identify creative fatigue, audience saturation, and provide actionable recommendations.

---

## Data Sources

### Primary API
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Get Meta Ads attribution data at multiple levels.

### API Calls Required (4 calls per period, 8 total for comparison)

#### 1. Channel Level (Meta Overview)
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
*Filter results where `channel` = "facebook", "meta", or "instagram"*

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
*Filter results where `channel` = "facebook", "meta", or "instagram"*

#### 3. Ad Set Level
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
*Filter results where `channel` = "facebook", "meta", or "instagram"*

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
*Filter results where `channel` = "facebook", "meta", or "instagram"*

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Linear attribution |
| `goal` | `purchase` | Purchase conversions |
| `page_size` | `100` | Get all records |
| `channel_filter` | `facebook`, `meta`, `instagram` | Filter for Meta only |

---

## Available Metrics from API

### 🌟 Attribuly-Exclusive Metrics (NOT in Meta Ads Manager)

| Metric | Field Name | Description | Why It Matters |
|--------|------------|-------------|----------------|
| **ncROAS** | `new_order_roas` | New Customer ROAS = new_order_conversion_value / spend | **TRUE incrementality** - excludes repeat buyers |
| **ncRevenue** | `new_order_conversion_value` | Revenue from first-time buyers only | Shows **incremental revenue** from ads |
| **ncConversions** | `new_order_conversions` | New customer purchase count | Measures **real acquisition**, not repeat orders |
| **Profit** | `profit` | Net profit = Revenue - COGS - Spend | **Real profitability**, not vanity ROAS |
| **Margin** | `margin` | Profit margin % = profit / revenue | Identifies **profitable vs unprofitable** campaigns |
| **LTV** | `ltv` | Customer Lifetime Value | Long-term ROI for **sustainable growth** |
| **True ROAS** | `roas` | Attribuly first-party attributed ROAS | **Accurate attribution** vs inflated Meta ROAS |
| **View-Through Analysis** | `view_through_roas` | Separated view-through attribution | See **true click vs view impact** |
| **Leads** | `new_lead_conversions` | Lead/email subscriber conversions | Track **top-of-funnel** acquisition |

### Standard Metrics

| Metric | Field Name | Description |
|--------|------------|-------------|
| **Channel** | `channel` | "facebook", "meta", or "instagram" |
| **Campaign** | `campaign` | Campaign name |
| **Ad Set** | `ad_set` | Ad Set name |
| **Ad Name** | `ad_name` | Ad name |
| **Conversions** | `conversions` | Attribuly-attributed purchases |
| **Revenue** | `conversion_value` | Attribuly-attributed revenue |
| **Platform Revenue** | `ad_net_conversion_value` | Meta-reported conversion value |
| **Platform ROAS** | `ad_net_roas` | Meta-reported ROAS |
| **Spend** | `spend` | Ad spend |
| **CPA** | `cpa` | Cost Per Acquisition |
| **Impressions** | `impressions` | Total impressions |
| **CPM** | `cpm` | Cost per 1000 impressions |
| **Clicks** | `clicks` | Total clicks |
| **CVR** | `cvr` | Conversion Rate |
| **CPC** | `cpc` | Cost Per Click |
| **CTR** | `ctr` | Click-Through Rate |
| **Outbound Clicks** | `outbound_clicks` | Clicks that leave Meta |
| **Outbound CTR** | `outbound_ctr` | Outbound Click-Through Rate |
| **Cost per Outbound Click** | `cost_per_outbound_click` | Cost per outbound click |
| **View-Through Conversions** | `view_through_conversions` | 1-day view conversions |
| **View-Through Value** | `view_through_conversion_value` | Value from view-through |

---

## Execution Steps

### Step 1: Fetch Meta Ads Data at All Levels
Make 4 API calls for **Current Period** (last 7 days):
1. Channel level (to get Meta totals)
2. Campaign level
3. Ad Set level
4. Ad level

### Step 2: Fetch Comparison Data
Make 4 API calls for **Previous Period** (prior 7 days).

### Step 3: Filter for Meta Only
Filter all results where `channel` contains "facebook", "meta", or "instagram".

### Step 4: Calculate WoW Changes
For each campaign/ad set/ad, calculate:
```
change_percent = ((current_value - previous_value) / previous_value) * 100
```

### Step 5: Segment Campaigns by Type
Categorize campaigns into:
- **Prospecting**: Campaign name contains "prospecting", "cold", "tofu", "acquisition"
- **Retargeting**: Campaign name contains "retargeting", "remarketing", "warm", "mofu", "bofu"
- **Advantage+ (ASC)**: Campaign name contains "advantage", "asc", "shopping"
- **Lookalike**: Campaign name contains "lookalike", "lal", "similar"
- **Interest-Based**: Campaign name contains "interest", "broad"
- **Other**: Everything else

### Step 6: Identify Issues
Flag campaigns/ad sets/ads with:
- ROAS < Target ROAS
- CPA > Target CPA
- CTR < 0.8% (for prospecting)
- Outbound CTR < 0.5%
- CVR < 1.5%
- CPM > $20 (for prospecting)
- Spend > $100 with 0 conversions

### Step 7: Detect Creative Fatigue
Flag ads where:
- CTR dropped >20% WoW
- CPM increased >15% WoW
- Estimated frequency > 3 (if available)

### Step 8: Generate Recommendations
Based on issues found, provide specific actions.

---

## Meta Ads Specific Analysis

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
| >80% | ✅ Highly Incremental | Scale budget aggressively |
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

### Prospecting vs. Retargeting Analysis (Enhanced with Attribuly Metrics)
| Metric | Prospecting | Retargeting | Benchmark | Attribuly Insight |
|--------|-------------|-------------|-----------|-------------------|
| ROAS | Target (2-3x) | Usually high (>4x) | Prospecting drives growth | Compare to ncROAS |
| **ncROAS** | **Should be HIGH** | **Should be LOW** | ncROAS is the TRUE metric | **Prospecting = new customers** |
| CPA | Higher acceptable | Should be low | Prospecting CPA is key | Check ncCPA |
| **Profit** | Lower margin OK | High margin | Both should be profitable | **Real bottom line** |
| CPM | $10-20 | $15-30 | Higher for warm audiences | - |
| CTR | >0.8% | >1.5% | Warm audiences click more | - |
| **LTV** | **Should be HIGH** | Lower (repeat buyers) | Prospecting LTV is key | **Long-term value** |

**Key Insight (Attribuly Exclusive):** 
- If Retargeting ROAS is high but Retargeting **ncROAS is low** → Just capturing existing customers (not incremental)
- If Prospecting ROAS is low but Prospecting **ncROAS is similar** → Actually acquiring new customers (good!)
- **Always prioritize ncROAS over ROAS for growth decisions**
- **Check LTV** — prospecting should bring high-LTV customers, not one-time discount buyers

### Advantage+ Shopping Campaign (ASC) Incrementality Analysis (Attribuly Exclusive)
ASC campaigns require special attention with Attribuly metrics:

| Check | Formula | Interpretation |
|-------|---------|----------------|
| **Incrementality Gap** | `(roas - ncROAS) / roas × 100` | >50% = cannibalizing existing customers |
| **Attribution Inflation** | `(ad_net_roas - roas) / ad_net_roas × 100` | >40% = Meta severely over-attributing |
| **Profit Check** | `profit > 0` | Negative = losing money despite "good ROAS" |
| **New Customer %** | `new_order_conversion_value / conversion_value × 100` | <50% = not driving acquisition |
| **LTV Check** | `ltv > avg_ltv` | Low LTV = acquiring low-quality customers |

**ASC Decision Matrix:**
| ncROAS | Profit | LTV | Action |
|--------|--------|-----|--------|
| High | Positive | High | ✅ Scale aggressively |
| High | Positive | Low | 🟡 Scale cautiously, monitor retention |
| High | Negative | Any | 🟠 Check COGS, optimize products |
| Low | Positive | Any | 🟠 Reduce budget, improve audience signals |
| Low | Negative | Any | 🔴 Pause or restructure |

### View-Through Attribution Analysis (Attribuly Exclusive)
- Compare `roas` vs. `view_through_roas` — large gap indicates heavy reliance on view-through.
- View-through is often inflated; prioritize click-based attribution.
- **Attribuly separates click vs view** — use this to understand true impact.

| View-Through Gap | Interpretation | Action |
|------------------|----------------|--------|
| <20% | ✅ Healthy | Click-driven conversions |
| 20-40% | 🟡 Moderate | Monitor, may be OK for awareness |
| >40% | 🔴 Inflated | Don't trust Meta ROAS, use Attribuly |

---

## Root Cause Analysis Logic (Enhanced with Attribuly Metrics)

### Scenario 1: Overall Meta ROAS Dropped
**Check in order:**
1. **CPM increased?** → Audience saturation or competition
2. **CTR decreased?** → Creative fatigue
3. **CVR decreased?** → Landing page or offer issue
4. **Specific campaign dropped?** → Campaign-specific problem

### Scenario 2: CPA Increased
**Check in order:**
1. **CPM increased?** → Audience saturation
2. **CTR decreased?** → Creative fatigue
3. **CVR decreased?** → Landing page issue
4. **Outbound CTR low?** → Ad not driving quality traffic

### Scenario 3: Spend Increased but Revenue Flat
**Check:**
1. Which campaign type increased spend?
2. Is it a new campaign in learning phase?
3. Is ASC cannibalizing retargeting?

### Scenario 4: High Platform ROAS, Low Attribuly ROAS
**Diagnosis:** Meta is over-attributing (likely view-through or retargeting)
**Action:** Check if conversions are truly incremental using ncROAS.

### Scenario 5: Creative Fatigue Detected
**Symptoms:**
- CTR dropping WoW
- CPM increasing WoW
- Frequency > 3
**Action:** Pause fatigued ads, launch new creative variations.

### 🆕 Scenario 6: High ROAS but Low ncROAS (Attribuly Exclusive)
**Diagnosis:** Campaign is capturing existing customers, not acquiring new ones
**Root Cause Analysis:**
1. **Check ncROAS / ROAS ratio** — if <50%, campaign is cannibalizing
2. **Check audience overlap** — retargeting audiences may be too broad
3. **Check Lookalike source** — may be based on all customers, not just high-value ones

**Action:**
- Exclude existing customers from prospecting campaigns
- Narrow Lookalike to high-LTV customers only
- Shift budget to campaigns with higher ncROAS

### 🆕 Scenario 7: Positive ROAS but Negative Profit (Attribuly Exclusive)
**Diagnosis:** Revenue looks good but COGS is eating margins
**Root Cause Analysis:**
1. **Check margin by campaign** — identify low-margin campaigns
2. **Check product mix** — are low-margin products being promoted?
3. **Check discount usage** — heavy discounting may hurt profit
4. **Check creative** — discount-focused creatives attract margin-killers

**Action:**
- Exclude low-margin products from campaigns
- Create separate campaigns for high-margin products
- Reduce discount-focused creatives
- Use value-based bidding with profit signals

### 🆕 Scenario 8: Low LTV Customers from Ads (Attribuly Exclusive)
**Diagnosis:** Ads are acquiring low-quality customers who don't return
**Root Cause Analysis:**
1. **Compare LTV by campaign** — identify campaigns with low LTV
2. **Check discount dependency** — discount-driven customers have lower LTV
3. **Check product category** — some products attract one-time buyers
4. **Check audience quality** — broad targeting may bring low-intent users

**Action:**
- Optimize for value-based bidding using LTV data
- Exclude heavy discount seekers
- Promote products with higher repeat purchase rates
- Narrow targeting to high-intent audiences

### 🆕 Scenario 9: Prospecting ncROAS Lower than Retargeting ncROAS (Attribuly Exclusive)
**Diagnosis:** This is EXPECTED — but check the gap
**Analysis:**
- Prospecting ncROAS should be 60-80% of Retargeting ncROAS
- If gap is >50%, prospecting may be too broad
- If gap is <20%, retargeting may be cannibalizing

**Action:**
- If prospecting ncROAS is too low: narrow audiences, improve creative
- If retargeting ncROAS is too high: check for brand cannibalization

---

## Output Format

```markdown
# Meta Ads Performance Report
**Period:** [Current Start Date] to [Current End Date]
**Compared to:** [Previous Start Date] to [Previous End Date]
**Attribution Model:** Linear

---

## 📊 Meta Ads Overview

| Metric | This Period | Last Period | Change | Status |
|--------|-------------|-------------|--------|--------|
| Revenue | $XX,XXX | $XX,XXX | +X.X% | ✅/🟡/🔴 |
| Conversions | XXX | XXX | +X.X% | ✅/🟡/🔴 |
| Spend | $X,XXX | $X,XXX | +X.X% | ✅/🟡/🔴 |
| ROAS (Attribuly) | X.XX | X.XX | +X.X% | ✅/🟡/🔴 |
| ROAS (Meta) | X.XX | X.XX | +X.X% | ⚠️ Compare |
| CPA | $XX.XX | $XX.XX | +X.X% | ✅/🟡/🔴 |
| CPM | $XX.XX | $XX.XX | +X.X% | ✅/🟡/🔴 |
| CTR | X.XX% | X.XX% | +X.X% | ✅/🟡/🔴 |

### 🌟 Attribuly-Exclusive Insights

| Metric | This Period | Last Period | Change | Status |
|--------|-------------|-------------|--------|--------|
| **ncROAS** | X.XX | X.XX | +X.X% | ✅/🟡/🔴 |
| **ncRevenue** | $XX,XXX | $XX,XXX | +X.X% | ✅/🟡/🔴 |
| **Profit** | $X,XXX | $X,XXX | +X.X% | ✅/🟡/🔴 |
| **Margin** | XX.X% | XX.X% | +X.X% | ✅/🟡/🔴 |
| **Avg LTV** | $XXX | $XXX | +X.X% | ✅/🟡/🔴 |

**Attribution Gap:** Attribuly ROAS is X.X% [higher/lower] than Meta-reported ROAS.
**Incrementality Score:** X.X% of revenue is from NEW customers (ncROAS/ROAS).
**Profitability:** [Profitable/Break-even/Unprofitable] with $X,XXX net profit.
**View-Through Dependency:** X.X% of Meta-reported conversions are view-through only.

---

## 📈 Performance by Campaign Type

| Type | Revenue | ROAS | ncROAS | Profit | Margin | LTV | Incrementality | Status |
|------|---------|------|--------|--------|--------|-----|----------------|--------|
| Prospecting | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX | XX% | ✅ |
| Retargeting | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX | XX% | 🟡 |
| Advantage+ (ASC) | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX | XX% | 🔴 |
| Lookalike | $XX,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX | XX% | ✅ |

*Incrementality = ncROAS / ROAS × 100 (higher = more new customers)*
*LTV = Average lifetime value of customers acquired by this campaign type*

---

## 🏆 Top Performing Campaigns (by Profit - Attribuly Exclusive)

| Campaign | Type | Revenue | ROAS | ncROAS | Profit | Margin | LTV |
|----------|------|---------|------|--------|--------|--------|-----|
| [Campaign 1] | Prospecting | $X,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX |
| [Campaign 2] | ASC | $X,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX |
| [Campaign 3] | Lookalike | $X,XXX | X.XX | X.XX | $X,XXX | XX% | $XXX |

*Ranked by Profit, not Revenue — shows TRUE business impact*
*High LTV campaigns should be prioritized for scaling*

---

## ⚠️ Underperforming Campaigns (Attribuly Analysis)

| Campaign | Type | Spend | ROAS | ncROAS | Profit | Issue | Recommendation |
|----------|------|-------|------|--------|--------|-------|----------------|
| [Campaign A] | Prospecting | $XXX | X.XX | X.XX | -$XXX | **Negative Profit** | Pause or optimize products |
| [Campaign B] | ASC | $XXX | X.XX | X.XX | $XXX | **Low ncROAS** | Cannibalizing - fix targeting |
| [Campaign C] | Retargeting | $XXX | X.XX | X.XX | $XXX | **Low LTV** | Acquiring low-quality customers |

*Issues prioritized by Profit and LTV impact, not just ROAS*

---

## 🔍 Ad Set Analysis (Top Issues)

| Ad Set | Campaign | Spend | ROAS | CPM | CTR | Issue |
|--------|----------|-------|------|-----|-----|-------|
| [Ad Set 1] | [Campaign] | $XXX | X.XX | $XX | X.X% | High CPM |
| [Ad Set 2] | [Campaign] | $XXX | X.XX | $XX | X.X% | Low CTR |

---

## 🎨 Ad-Level Creative Analysis

### Top Performing Ads
| Ad | Campaign | Clicks | CTR | CVR | ROAS |
|----|----------|--------|-----|-----|------|
| [Ad 1] | [Campaign] | XXX | X.X% | X.X% | X.XX |

### 🚨 Creative Fatigue Detected
| Ad | Campaign | CTR Change | CPM Change | Action |
|----|----------|------------|------------|--------|
| [Ad A] | [Campaign] | -25% | +18% | **PAUSE** |
| [Ad B] | [Campaign] | -20% | +12% | **MONITOR** |

### Underperforming Ads (Spend > $50, ROAS < 1)
| Ad | Campaign | Spend | Conversions | Action |
|----|----------|-------|-------------|--------|
| [Ad X] | [Campaign] | $XXX | 0 | Pause |

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

## 📋 Advantage+ (ASC) Deep Dive (Attribuly Incrementality Analysis)

| ASC Campaign | Spend | ROAS | ncROAS | Profit | Margin | LTV | Incrementality | Status |
|--------------|-------|------|--------|--------|--------|-----|----------------|--------|
| [ASC 1] | $XXX | X.XX | X.XX | -$XXX | -X% | $XX | XX% | 🔴 Cannibalizing + Unprofitable |
| [ASC 2] | $XXX | X.XX | X.XX | $XXX | XX% | $XXX | XX% | ✅ Incremental + Profitable |

**ASC Incrementality Score:** X.X% of ASC revenue is from new customers.
**ASC Profitability:** $X,XXX total profit at XX% margin.
**ASC LTV Quality:** Average LTV of $XXX vs. overall average of $XXX.
**ASC vs Retargeting Overlap:** X.X% overlap with retargeting conversions.

### 🎯 ASC Optimization Recommendations (Attribuly-Powered)
1. **Scale** ASC campaigns with ncROAS > X.XX AND profit > $0 AND LTV > avg
2. **Pause** ASC campaigns with ncROAS < X.XX OR profit < $0
3. **Optimize** ASC campaigns with high ROAS but low ncROAS (add audience exclusions)
4. **Monitor** ASC campaigns with high ncROAS but low LTV (may be discount-driven)

---

## 📊 View-Through Attribution Analysis

| Metric | Click-Based | Incl. View-Through | Gap |
|--------|-------------|-------------------|-----|
| Conversions | XXX | XXX | +XX% |
| Revenue | $XX,XXX | $XX,XXX | +XX% |
| ROAS | X.XX | X.XX | +XX% |

**Recommendation:** [If gap >30%, recommend focusing on click-based metrics]
```

---

## Thresholds for Alerts

### Standard Metrics
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| ROAS Change | -10% to -20% | < -20% |
| CPA Change | +10% to +20% | > +20% |
| CPM (Prospecting) | > $15 | > $25 |
| CTR (Prospecting) | < 1% | < 0.5% |
| Outbound CTR | < 0.8% | < 0.5% |
| CVR | < 2% | < 1% |
| CTR Drop (Fatigue) | -15% to -25% | < -25% |
| Spend with 0 conversions | > $100 | > $300 |

### 🌟 Attribuly-Exclusive Thresholds
| Metric | Warning (🟡) | Critical (🔴) | Why It Matters |
|--------|--------------|---------------|----------------|
| **ncROAS** | < 1.5 | < 1.0 | Not acquiring new customers profitably |
| **Incrementality (ncROAS/ROAS)** | < 50% | < 30% | Cannibalizing existing customers |
| **Profit** | < $0 (break-even) | < -$500 | Losing money despite "good ROAS" |
| **Margin** | < 15% | < 10% | Low profitability, unsustainable |
| **LTV** | < $50 | < $30 | Acquiring low-quality customers |
| **Attribution Gap (Meta vs Attribuly)** | > 40% | > 60% | Meta severely over-attributing |
| **View-Through Dependency** | > 30% | > 50% | Conversions not truly incremental |
| **Prospecting ncROAS** | < 1.2 | < 0.8 | Not driving profitable acquisition |

---

## Complete Data Retrieval Workflow

### Step 0: Discover Connected Meta Ad Accounts
Before querying Meta Ads data, retrieve the connected account ID.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/connection/source" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "platform_type": "facebook"
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
        "account_id": "act_123456789",
        "name": "My Store - Meta Ads",
        "platform_type": "facebook",
        "currency": "USD",
        "connected": 1
      }
    ]
  }
}
```

**Extract `account_id`** (e.g., `act_123456789`) for use in subsequent queries.

---

## Enhanced Data APIs (Now Available)

### 1. Frequency & Reach (Creative Fatigue Detection)
**Purpose:** Detect ad fatigue and audience saturation. Critical for Meta optimization.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "act_123456789",
    "level": "ad",
    "fields": ["campaign_name", "campaign_id", "adset_name", "adset_id", "ad_name", "ad_id", "impressions", "reach", "frequency", "spend", "clicks", "cpm", "cpc", "ctr"],
    "time_range": {
      "since": "2025-03-10",
      "until": "2025-03-16"
    }
  }'
```

**Key Fields:**
| Field | Description |
|-------|-------------|
| `reach` | Unique users who saw the ad |
| `frequency` | Average times each person saw the ad (impressions/reach) |

**Frequency Thresholds for Fatigue Detection:**
| Frequency | Status | Action |
|-----------|--------|--------|
| 1-2 | ✅ Healthy | Continue |
| 2-3 | 🟡 Monitor | Watch for CTR decline |
| 3-5 | 🟠 Warning | Consider refreshing creative |
| >5 | 🔴 Fatigued | Pause or replace creative |

### 2. Video Metrics (ThruPlay, Hook Rate, Hold Rate)
**Purpose:** Analyze video ad performance. Calculate Hook Rate and Hold Rate.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "act_123456789",
    "level": "ad",
    "fields": ["ad_name", "ad_id", "impressions", "reach", "video_play_actions", "video_p25_watched_actions", "video_p50_watched_actions", "video_p75_watched_actions", "video_p95_watched_actions", "video_p100_watched_actions", "video_thruplay_watched_actions", "video_avg_time_watched_actions", "video_continuous_2_sec_watched_actions", "video_30_sec_watched_actions"],
    "time_range": {
      "since": "2025-03-10",
      "until": "2025-03-16"
    }
  }'
```

**Calculated Metrics:**
| Metric | Formula | Benchmark |
|--------|---------|-----------|
| **Hook Rate** | `video_continuous_2_sec_watched / impressions × 100` | 20-25% good, >30% excellent |
| **Hold Rate** | `video_thruplay / video_play_actions × 100` | 40-50% good, >50% excellent |
| **ThruPlay Rate** | `video_thruplay / impressions × 100` | Varies by video length |

### 3. Placement Breakdown
**Purpose:** Optimize budget allocation across placements (Feed, Stories, Reels).

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "act_123456789",
    "level": "ad",
    "fields": ["campaign_name", "adset_name", "ad_name", "impressions", "reach", "clicks", "spend", "cpm", "ctr", "conversions", "cost_per_conversion"],
    "breakdowns": ["publisher_platform", "platform_position"],
    "time_range": {
      "since": "2025-03-10",
      "until": "2025-03-16"
    }
  }'
```

**Placement Values:**
| Platform | Positions |
|----------|-----------|
| `facebook` | feed, story, reels, marketplace, video_feeds, right_hand_column |
| `instagram` | feed, story, reels, explore |
| `messenger` | inbox, story |
| `audience_network` | classic, rewarded_video |

### 4. Demographic Breakdown (Age, Gender)
**Purpose:** Analyze performance by age and gender to optimize targeting.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "act_123456789",
    "level": "ad",
    "fields": ["campaign_name", "adset_name", "ad_name", "impressions", "reach", "clicks", "spend", "conversions", "cost_per_conversion"],
    "breakdowns": ["age", "gender"],
    "time_range": {
      "since": "2025-03-10",
      "until": "2025-03-16"
    }
  }'
```

**Age Buckets:** `13-17`, `18-24`, `25-34`, `35-44`, `45-54`, `55-64`, `65+`
**Gender Values:** `male`, `female`, `unknown`

**Note:** When using `age` or `gender` breakdowns with `reach`, data is limited to 13 months (394 days).

### 5. Device Breakdown
**Purpose:** Optimize by device type (mobile, desktop, tablet).

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "act_123456789",
    "level": "ad",
    "fields": ["campaign_name", "adset_name", "ad_name", "impressions", "clicks", "spend", "conversions"],
    "breakdowns": ["impression_device"],
    "time_range": {
      "since": "2025-03-10",
      "until": "2025-03-16"
    }
  }'
```

**Device Values:** `desktop`, `iphone`, `ipad`, `android_smartphone`, `android_tablet`, `other`

---

## Error Handling & Logging

### Validate API Response
```javascript
function validateMetaQueryResponse(response) {
  console.log(JSON.stringify({
    timestamp: new Date().toISOString(),
    level: 'DEBUG',
    skill: 'meta_ads_performance',
    action: 'validate_response',
    response_code: response.code
  }));

  if (response.code !== 1) {
    console.error(JSON.stringify({
      timestamp: new Date().toISOString(),
      level: 'ERROR',
      skill: 'meta_ads_performance',
      action: 'api_error',
      error: response.message
    }));
    return { success: false, error: response.message };
  }

  if (!response.data || response.data.length === 0) {
    console.warn(JSON.stringify({
      timestamp: new Date().toISOString(),
      level: 'WARN',
      skill: 'meta_ads_performance',
      action: 'empty_results',
      message: 'No data returned from Meta API'
    }));
    return { success: true, data: [] };
  }

  console.log(JSON.stringify({
    timestamp: new Date().toISOString(),
    level: 'INFO',
    skill: 'meta_ads_performance',
    action: 'query_success',
    result_count: response.data.length
  }));

  return { success: true, data: response.data };
}
```

### Rate Limiting
| API | Limit | Recommendation |
|-----|-------|----------------|
| Meta Query API | 200 calls per hour per ad account | Batch requests, use async reports for large data |
| Meta Query API | 4,800 calls per day per ad account | Plan daily query budget |
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
      if (error.code === 429) {
        console.warn('[RATE_LIMIT] Meta API rate limit hit, backing off...');
        await new Promise(r => setTimeout(r, 60000)); // Wait 1 minute
        continue;
      }
      if (attempt === maxRetries - 1) throw error;
    }
  }
}
```

---

## Standard Attribuly API Calls

### Get Meta Campaign Performance (Current Period)
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

### Get Meta Ad Set Performance
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

### Get Meta Ad Level Performance
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
| `meta_creative_analysis` | When creative fatigue detected |
| `budget_optimization` | When budget reallocation needed |
| `funnel_analysis` | When CVR issues detected |
| `weekly_marketing_performance` | For cross-channel context |
