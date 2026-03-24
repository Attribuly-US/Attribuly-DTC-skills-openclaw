---
name: audience-optimization
version: 1.0.0
description: Stop ecommerce ad cannibalization. Optimize prospecting and retargeting audiences for your Shopify brand using Attribuly's advanced LTV insights.
---
# Skill: AllyClaw Audience Optimization for DTC

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly-powered audience optimization is superior:**

| Metric | Native Ads Managers | Attribuly | Why It Matters |
|--------|---------------------|-----------|----------------|
| **ncROAS** | ❌ Not available | ✅ `new_order_roas` | See which audiences acquire NEW customers |
| **New vs Repeat** | ❌ Not available | ✅ `new_lead_conversions` | Identify audiences hitting existing customers |
| **LTV by Audience** | ❌ Not available | ✅ `ltv` | Find audiences that bring high-value customers |
| **Cross-Channel View** | ⚠️ Siloed | ✅ Unified | Detect overlap across Google & Meta |
| **True Incrementality** | ❌ Not available | ✅ Calculated | Stop wasting budget on cannibalization |

**Key Insight:** Most DTC brands waste 20-40% of their ad spend targeting the same users across multiple campaigns and platforms. Attribuly reveals which audiences are truly incremental vs. which are just stealing credit from each other.

---

## When to Trigger This Skill

### Automatic Triggers
- **Low Incrementality**: When ncROAS / ROAS < 0.5 (>50% repeat customers)
- **Audience Saturation**: When frequency > 4 and CTR declining
- **Diminishing Returns**: When ROAS drops >15% as spend increases
- **High Overlap Suspected**: When multiple campaigns have similar performance patterns

### Manual Triggers (User Commands)
- "Optimize audiences"
- "Audience overlap"
- "Which audiences are working?"
- "Stop cannibalization"
- "Audience recommendations"
- "Who should I target?"
- "Lookalike performance"
- "Retargeting vs prospecting"

### Context Triggers
- After budget optimization reveals low incrementality
- When ncROAS is significantly lower than ROAS
- When scaling campaigns hit diminishing returns
- When launching new prospecting campaigns

---

## Skill Purpose

Provide **audience-level performance analysis and optimization recommendations** to:
1. **Maximize New Customer Acquisition** (ncROAS)
2. **Reduce Audience Overlap** across campaigns
3. **Improve LTV** by targeting high-value customer profiles
4. **Eliminate Cannibalization** between prospecting and retargeting

---

## Data Sources

### Primary APIs

#### 1. Get Conversion Goals (Initialize)
**Endpoint:** `POST /{version}/api/get/setting-goals`
**Purpose:** Fetch available conversion goals dynamically.

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/setting-goals" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{}'
```

#### 2. Get Campaign-Level Performance
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Analyze performance by campaign (proxy for audience).

```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimensions": ["channel", "campaign"],
  "model": "linear",
  "goal": "purchase",
  "orders": [{"column": "spend", "order": "desc"}],
  "page": 1,
  "page_size": 100
}
```

#### 3. Get Ad Set-Level Performance
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Analyze performance by ad set (audience level for Meta).

```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimensions": ["channel", "campaign", "ad_set"],
  "model": "linear",
  "goal": "purchase",
  "orders": [{"column": "spend", "order": "desc"}],
  "page": 1,
  "page_size": 100
}
```

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Linear attribution |
| `goal` | Dynamic from Settings API | Use `checkout_completed` event type |
| `lookback_period` | 14 days | Enough data for audience analysis |
| `min_spend_threshold` | $50 | Minimum spend to include audience |

---

## Key Metrics for Audience Optimization

### 🌟 Attribuly-Exclusive Metrics (PRIMARY)
| Metric | Field Name | Audience Use |
|--------|------------|--------------|
| **ncROAS** | `new_order_roas` | Identify audiences acquiring NEW customers |
| **ncRevenue** | `new_order_conversion_value` | Revenue from new customers |
| **LTV** | `ltv` | Customer quality from audience |
| **Incrementality** | `ncROAS / ROAS` | Detect cannibalization |
| **Profit** | `profit` | Audience profitability |
| **Leads** | `new_lead_conversions` | Lead/email subscriber count from audience |

### Standard Metrics
| Metric | Field Name | Audience Use |
|--------|------------|--------------|
| **ROAS** | `roas` | Baseline efficiency |
| **CPA** | `cpa` | Cost to acquire from audience |
| **CPM** | `cpm` | Cost to reach audience |
| **CTR** | `ctr` | Audience engagement |
| **CVR** | `cvr` | Audience conversion rate |

### Calculated Metrics
| Metric | Formula | Purpose |
|--------|---------|---------|
| **New Customer %** | `new_order_conversion_value / conversion_value` | % of revenue from new customers |
| **Audience Efficiency** | `ncROAS / CPM × 1000` | Value per impression |
| **LTV:CAC Ratio** | `ltv / cpa` | Long-term audience value |

---

## Audience Classification Framework

### By Funnel Stage

| Audience Type | Characteristics | Expected ncROAS | Expected ROAS |
|---------------|-----------------|-----------------|---------------|
| **Cold Prospecting** | No prior interaction | High (>80% new) | Lower (1.5-2.5x) |
| **Warm Prospecting** | Engaged but not purchased | Medium (50-80% new) | Medium (2-3x) |
| **Retargeting** | Cart abandoners, viewers | Low (<30% new) | High (3-5x) |
| **Existing Customers** | Past purchasers | Very Low (<10% new) | Very High (>5x) |

### By Performance

| Classification | Criteria | Action |
|----------------|----------|--------|
| **🚀 Scale** | ncROAS > 1.5 AND LTV > avg AND New Customer % > 60% | Increase budget, expand lookalikes |
| **✅ Maintain** | ncROAS 1.0-1.5 AND profitable | Keep current, monitor |
| **🟡 Optimize** | ncROAS 0.8-1.0 OR New Customer % 30-60% | Narrow targeting, refresh creative |
| **🔴 Reduce** | ncROAS < 0.8 OR New Customer % < 30% | Reduce budget, add exclusions |
| **⏸️ Pause** | ncROAS < 0.5 AND losing money | Pause, restructure |

---

## Audience Optimization Logic

### Step 1: Classify Audiences by Type
```
For each campaign/ad set:
1. Identify audience type from naming convention or structure
2. Calculate New Customer % = new_lead_conversions / conversions
3. Classify as: Prospecting (>60%), Warm (30-60%), Retargeting (<30%)
```

### Step 2: Analyze Incrementality
```
For each audience:
- Incrementality Score = ncROAS / ROAS
- If Incrementality < 0.5 → Cannibalizing
- If Incrementality > 0.8 → Truly incremental
```

### Step 3: Detect Overlap Issues
```
Overlap Indicators:
1. Multiple campaigns with similar ncROAS patterns
2. Retargeting ncROAS higher than expected (>0.5)
3. Prospecting ncROAS lower than expected (<0.6)
4. Total conversions > unique customers (cross-campaign)
```

### Step 4: Analyze LTV by Audience
```
For each audience:
- Calculate avg LTV
- Compare to overall avg LTV
- Flag audiences with LTV < 70% of average
```

### Step 5: Generate Recommendations
Prioritize by:
1. **Incrementality Impact** (stop cannibalization first)
2. **LTV Improvement** (target better customers)
3. **Scale Opportunity** (expand what works)

---

## Audience Optimization Strategies

### Strategy 1: Prospecting vs Retargeting Balance

**Optimal Split (by spend):**
| Stage | Spend Share | Expected ncROAS | Purpose |
|-------|-------------|-----------------|---------|
| Cold Prospecting | 50-60% | 1.5-2.5x | Growth engine |
| Warm Prospecting | 20-30% | 2.0-3.0x | Nurture interested |
| Retargeting | 15-25% | 3.0-5.0x | Capture ready buyers |

**Red Flags:**
- Retargeting > 40% of spend → Over-reliance on existing demand
- Prospecting ncROAS < 1.0 → Targeting too broad
- Retargeting ncROAS > 0.5 → Audience too broad (hitting new customers)

### Strategy 2: Lookalike Optimization

**Lookalike Quality Ladder:**
| Source | Expected Quality | Use Case |
|--------|------------------|----------|
| High-LTV Customers | ⭐⭐⭐⭐⭐ | Primary prospecting |
| Repeat Purchasers | ⭐⭐⭐⭐ | Secondary prospecting |
| All Purchasers | ⭐⭐⭐ | Broad prospecting |
| Add to Cart | ⭐⭐ | Volume play |
| Website Visitors | ⭐ | Awareness only |

**Recommendation Logic:**
```
If Lookalike ncROAS < 1.2:
  → Source audience may be too broad
  → Recommend: Create lookalike from high-LTV customers only
  
If Lookalike LTV < avg LTV:
  → Attracting low-quality customers
  → Recommend: Narrow lookalike % or change source
```

### Strategy 3: Exclusion Strategy

**Must-Have Exclusions:**
| Campaign Type | Exclude |
|---------------|---------|
| Prospecting | All purchasers (30-180 days) |
| Cold Prospecting | Website visitors (7-14 days) |
| Lookalikes | Existing customers |
| Retargeting | Recent purchasers (7-14 days) |

**Exclusion Impact Analysis:**
```
If Prospecting ncROAS increases after adding exclusions:
  → Confirms overlap was occurring
  → Expand exclusion window

If Prospecting volume drops significantly:
  → Audience was heavily overlapping
  → Need to find new prospecting sources
```

---

## Output Format

```markdown
# 👥 Audience Optimization Analysis
**Analysis Period:** [Start Date] to [End Date]
**Total Audiences Analyzed:** XX
**Overall New Customer %:** XX%

---

## 📊 Audience Performance Overview

### By Funnel Stage

| Stage | Spend | Share | ncROAS | New Customer % | LTV | Status |
|-------|-------|-------|--------|----------------|-----|--------|
| Cold Prospecting | $X,XXX | XX% | X.XX | XX% | $XXX | ✅/🟡/🔴 |
| Warm Prospecting | $X,XXX | XX% | X.XX | XX% | $XXX | ✅/🟡/🔴 |
| Retargeting | $X,XXX | XX% | X.XX | XX% | $XXX | ✅/🟡/🔴 |

**Ideal Split:** 50-60% Prospecting, 20-30% Warm, 15-25% Retargeting

---

## 🎯 Top Performing Audiences (by ncROAS)

| Audience/Ad Set | Channel | Spend | ncROAS | New Customer % | LTV | Action |
|-----------------|---------|-------|--------|----------------|-----|--------|
| [Audience 1] | Meta | $X,XXX | X.XX | XX% | $XXX | 🚀 Scale |
| [Audience 2] | Google | $X,XXX | X.XX | XX% | $XXX | 🚀 Scale |

---

## ⚠️ Cannibalization Detected

| Audience/Ad Set | Channel | ncROAS | ROAS | Incrementality | Issue |
|-----------------|---------|--------|------|----------------|-------|
| [Audience A] | Meta | X.XX | X.XX | XX% | Hitting existing customers |
| [Audience B] | Google | X.XX | X.XX | XX% | Overlap with retargeting |

**Estimated Wasted Spend:** $X,XXX (XX% of total)

---

## 🔍 Overlap Analysis

### Cross-Campaign Overlap
- **Prospecting ↔ Retargeting Overlap:** XX% estimated
- **Google ↔ Meta Overlap:** XX% estimated

### Recommendations to Reduce Overlap
1. Add purchaser exclusions to prospecting campaigns
2. Narrow retargeting window from XX days to XX days
3. Create separate campaigns for different funnel stages

---

## 💡 Optimization Recommendations

### High Priority

#### 1. Scale High-ncROAS Audiences
| Audience | Current Spend | Recommended | Expected Impact |
|----------|---------------|-------------|-----------------|
| [Audience X] | $X,XXX | $X,XXX (+XX%) | +XX new customers |

#### 2. Fix Cannibalization
| Action | Audience | Expected Savings |
|--------|----------|------------------|
| Add exclusions | [Audience Y] | $XXX/week |
| Narrow targeting | [Audience Z] | $XXX/week |

### Medium Priority

#### 3. Improve LTV
| Audience | Current LTV | Target LTV | Strategy |
|----------|-------------|------------|----------|
| [Audience W] | $XX | $XXX | Use high-LTV lookalike source |

---

## 📋 Action Items

### This Week
1. **Add purchaser exclusions** to all prospecting campaigns
2. **Scale [Audience X]** by 20%
3. **Reduce [Audience Y]** by 30%

### Next 2 Weeks
4. **Create new lookalike** from high-LTV customers
5. **Test narrower retargeting window** (7 days vs 14 days)

### Monitor
6. **Track ncROAS changes** after exclusions added
7. **Watch for volume drops** in prospecting
```

---

## Thresholds for Alerts

### Incrementality Thresholds
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| Incrementality (ncROAS/ROAS) | < 0.6 | < 0.4 |
| New Customer % (Prospecting) | < 60% | < 40% |
| New Customer % (Retargeting) | > 40% | > 50% |
| Retargeting Spend Share | > 35% | > 45% |

### LTV Thresholds
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| Audience LTV vs Average | < 80% | < 60% |
| LTV:CAC Ratio | < 2.0 | < 1.5 |

### Performance Thresholds
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| ncROAS (Prospecting) | < 1.2 | < 0.8 |
| ncROAS (Retargeting) | < 2.0 | < 1.5 |
| CPM Increase | > 20% | > 40% |

---

## Example API Calls

### 1. Get Ad Set Performance (Audience Level)
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-05",
    "end_date": "2025-03-18",
    "dimensions": ["channel", "campaign", "ad_set"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 100
  }'
```

### 2. Get Campaign Performance
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-05",
    "end_date": "2025-03-18",
    "dimensions": ["channel", "campaign"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "new_order_roas", "order": "desc"}],
    "page": 1,
    "page_size": 100
  }'
```

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `budget_optimization` | After audience changes, rebalance budget |
| `meta_ads_performance` | For Meta-specific audience analysis |
| `google_ads_performance` | For Google-specific audience analysis |
| `creative_fatigue_detector` | When audience saturation detected |
| `ltv_analysis` | For deeper LTV by audience analysis |

---

## Best Practices

### For the AI Agent
1. **Always calculate incrementality** — ncROAS / ROAS is the key metric
2. **Flag cannibalization first** — Biggest waste of budget
3. **Consider LTV** — Not just immediate ROAS
4. **Recommend exclusions** — Specific, actionable
5. **Show estimated savings** — Quantify the impact

### For the Client
1. **Add exclusions immediately** — Quick win
2. **Test one change at a time** — Isolate impact
3. **Monitor for 7 days** — Before making more changes
4. **Trust ncROAS** — Over platform-reported ROAS
5. **Prioritize high-LTV audiences** — Long-term value
