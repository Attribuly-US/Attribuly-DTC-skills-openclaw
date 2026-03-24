---
name: budget-optimization
version: 1.0.0
description: Maximize DTC profitability. Automatically reallocate budget across Meta and Google Ads using Attribuly's true ROAS for Shopify stores.
---
# Skill: AllyClaw Ecommerce Budget Optimizer

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly-powered budget optimization is superior:**

| Metric | Native Ads Managers | Attribuly | Why It Matters |
|--------|---------------------|-----------|----------------|
| **True ROAS** | ⚠️ Inflated 30-60% | ✅ First-party data | Allocate budget based on REAL performance |
| **ncROAS** | ❌ Not available | ✅ `new_order_roas` | Prioritize channels acquiring NEW customers |
| **Profit** | ❌ Not available | ✅ `profit` | Optimize for profit, not vanity revenue |
| **Cross-Channel View** | ⚠️ Siloed | ✅ Unified | See true channel contribution before reallocating |
| **Incrementality** | ❌ Not available | ✅ Calculated | Don't waste budget on cannibalization |

**Key Insight:** Most brands over-invest in retargeting and brand search because platforms over-attribute. Attribuly reveals where your INCREMENTAL growth actually comes from, enabling smarter budget allocation.

---

## When to Trigger This Skill

### Automatic Triggers
- **MER Off-Target**: When Marketing Efficiency Ratio deviates >15% from target
- **Spend Pacing Issues**: When daily/weekly spend is >30% over or under target
- **ROAS Threshold Breach**: When blended ROAS drops below minimum threshold for 3+ days
- **Budget Utilization**: When a channel is consistently under-spending (<70% of budget)

### Manual Triggers (User Commands)
- "Optimize budget"
- "Reallocate spend"
- "Where should I put more budget?"
- "Which campaigns should I scale?"
- "Budget recommendations"
- "How should I split my budget?"
- "MER is off, help me fix it"

### Context Triggers
- After weekly performance review shows inefficiencies
- When launching new campaigns and need to balance spend
- During seasonal peaks when budget flexibility is needed
- When overall profitability is declining despite stable ROAS

---

## Skill Purpose

Provide **data-driven budget reallocation recommendations** across channels and campaigns. The goal is to maximize:
1. **Profit** (not just revenue)
2. **New Customer Acquisition** (ncROAS)
3. **Marketing Efficiency Ratio (MER)**

While maintaining:
- Minimum spend thresholds for learning
- Diversification to reduce risk
- Sustainable scaling velocity

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

#### 2. Get Channel-Level Performance
**Endpoint:** `POST /{version}/api/all-attribution/get-list`
**Purpose:** Get performance breakdown by channel.

```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimensions": ["channel"],
  "model": "linear",
  "goal": "purchase",
  "orders": [{"column": "conversion_value", "order": "desc"}],
  "page": 1,
  "page_size": 100
}
```

#### 3. Get Campaign-Level Performance
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Get detailed campaign performance for reallocation decisions.

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

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Linear attribution |
| `goal` | Dynamic from Settings API | Use `checkout_completed` event type |
| `lookback_period` | 14 days | Enough data for stable recommendations |
| `min_spend_threshold` | $100 | Minimum spend to include in analysis |

---

## Key Metrics for Budget Optimization

### 🌟 Attribuly-Exclusive Metrics (PRIMARY)
| Metric | Field Name | Optimization Use |
|--------|------------|------------------|
| **ncROAS** | `new_order_roas` | Prioritize channels with high new customer acquisition |
| **ncRevenue** | `new_order_conversion_value` | Revenue from new customers |
| **Profit** | `profit` | Allocate more to profitable channels |
| **Margin** | `margin` | Ensure budget goes to sustainable channels |
| **Incrementality** | `ncROAS / ROAS` | Avoid channels cannibalizing existing customers |
| **Leads** | `new_lead_conversions` | Track lead/email subscriber acquisition |

### Standard Metrics
| Metric | Field Name | Optimization Use |
|--------|------------|------------------|
| **ROAS** | `roas` | Baseline efficiency metric |
| **CPA** | `cpa` | Cost efficiency |
| **Spend** | `spend` | Current allocation |
| **Revenue** | `conversion_value` | Volume metric |

### Calculated Metrics
| Metric | Formula | Purpose |
|--------|---------|---------|
| **MER** | `total_revenue / total_spend` | Overall marketing efficiency |
| **Spend Share** | `channel_spend / total_spend` | Current allocation % |
| **Revenue Share** | `channel_revenue / total_revenue` | Contribution % |
| **Efficiency Index** | `revenue_share / spend_share` | Over/under-indexed |

---

## Budget Optimization Logic

### Step 1: Calculate Current State
```
For each channel/campaign:
- Current Spend Share = spend / total_spend
- Current Revenue Share = revenue / total_revenue
- Efficiency Index = Revenue Share / Spend Share
- Incrementality Score = ncROAS / ROAS
- Profit Contribution = profit / total_profit
```

### Step 2: Classify Channels/Campaigns

| Classification | Criteria | Action |
|----------------|----------|--------|
| **🚀 Scale** | ncROAS > target AND profit > 0 AND Efficiency Index > 1.2 | Increase budget 15-25% |
| **✅ Maintain** | ncROAS ≈ target AND profit ≥ 0 AND Efficiency Index 0.8-1.2 | Keep current budget |
| **🟡 Optimize** | ncROAS < target BUT profit > 0 AND Efficiency Index 0.5-0.8 | Reduce 10-15%, optimize |
| **🔴 Reduce** | ncROAS < target AND profit < 0 OR Efficiency Index < 0.5 | Reduce 25-50% |
| **⏸️ Pause** | Spend > $200 AND conversions = 0 for 7+ days | Pause immediately |

### Step 3: Apply Constraints
- **Minimum Spend**: No channel below $50/day (learning threshold)
- **Maximum Concentration**: No single channel > 60% of total spend
- **Scaling Velocity**: Max 25% increase per week to avoid algorithm disruption
- **Diversification**: Maintain at least 2 active channels

### Step 4: Generate Recommendations
Prioritize recommendations by:
1. **Profit Impact** (highest first)
2. **Confidence Level** (more data = higher confidence)
3. **Risk Level** (lower risk first)

---

## Budget Reallocation Framework

### The Profit-First Allocation Model

```
Optimal Spend Share = (Profit Contribution × 0.4) + (ncROAS Rank × 0.3) + (Volume Potential × 0.3)

Where:
- Profit Contribution = channel_profit / total_profit
- ncROAS Rank = normalized rank (1 = best ncROAS)
- Volume Potential = headroom before diminishing returns
```

### Diminishing Returns Detection
```
If ROAS drops >15% when spend increases >20%:
  → Channel is hitting diminishing returns
  → Cap budget at current level
  → Reallocate excess to other channels
```

### Incrementality-Based Allocation
```
If ncROAS / ROAS < 0.5:
  → Channel is cannibalizing (>50% repeat customers)
  → Reduce budget by 20%
  → Shift to channels with ncROAS / ROAS > 0.7
```

---

## Output Format

```markdown
# 💰 Budget Optimization Recommendations
**Analysis Period:** [Start Date] to [End Date]
**Total Spend Analyzed:** $XX,XXX
**Current MER:** X.XX (Target: X.XX)

---

## 📊 Current Budget Allocation

| Channel | Spend | Share | Revenue | ROAS | ncROAS | Profit | Efficiency Index |
|---------|-------|-------|---------|------|--------|--------|------------------|
| Meta Ads | $X,XXX | XX% | $XX,XXX | X.XX | X.XX | $X,XXX | X.XX |
| Google Ads | $X,XXX | XX% | $XX,XXX | X.XX | X.XX | $X,XXX | X.XX |
| TikTok Ads | $X,XXX | XX% | $XX,XXX | X.XX | X.XX | $X,XXX | X.XX |

**Efficiency Index:** >1.0 = over-performing, <1.0 = under-performing

---

## 🎯 Recommended Budget Changes

### 🚀 Scale (Increase Budget)

| Channel/Campaign | Current | Recommended | Change | Reason |
|------------------|---------|-------------|--------|--------|
| [Campaign A] | $X,XXX | $X,XXX | +XX% | High ncROAS (X.XX), profitable, room to scale |

### 🔴 Reduce (Decrease Budget)

| Channel/Campaign | Current | Recommended | Change | Reason |
|------------------|---------|-------------|--------|--------|
| [Campaign B] | $X,XXX | $X,XXX | -XX% | Low ncROAS (X.XX), cannibalizing existing customers |

### ⏸️ Pause (Stop Spend)

| Channel/Campaign | Current Spend | Days Without Conversion | Recommendation |
|------------------|---------------|-------------------------|----------------|
| [Campaign C] | $XXX | X days | Pause immediately, investigate |

---

## 💡 Optimization Summary

| Metric | Current | After Optimization | Expected Change |
|--------|---------|-------------------|-----------------|
| Total Spend | $XX,XXX | $XX,XXX | +X% |
| Expected Revenue | $XX,XXX | $XX,XXX | +X% |
| Expected MER | X.XX | X.XX | +X% |
| Expected Profit | $X,XXX | $X,XXX | +X% |

---

## 📋 Action Items

### High Priority (This Week)
1. **[Action]**: [Specific recommendation with numbers]
   - *Expected Impact:* +$X,XXX profit
   - *Risk Level:* Low/Medium/High

### Medium Priority (Next 2 Weeks)
2. **[Action]**: [Specific recommendation]

### Monitor
3. **[Item to Watch]**: [What to track after changes]

---

## ⚠️ Constraints Applied

- ✅ No channel below $50/day minimum
- ✅ No single channel exceeds 60% of total spend
- ✅ Maximum 25% weekly increase per campaign
- ✅ At least 2 active channels maintained
```

---

## Thresholds for Alerts

### Budget Pacing
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| Spend > 130% of daily target | 🟡 Warning | Review and adjust |
| Spend > 150% of daily target | 🔴 Critical | Immediate intervention |
| Spend < 70% of daily target | 🟡 Warning | Check delivery issues |
| Spend < 50% of daily target | 🔴 Critical | Investigate immediately |

### Efficiency Thresholds
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| MER vs Target | -10% to -20% | < -20% |
| Channel ROAS | < 1.5 | < 1.0 |
| Channel ncROAS | < 1.2 | < 0.8 |
| Profit | Break-even | Negative |
| Efficiency Index | < 0.7 | < 0.5 |

---

## Example API Calls

### 1. Get Channel Performance (Last 14 Days)
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-04",
    "end_date": "2025-03-18",
    "dimensions": ["channel"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 100
  }'
```

### 2. Get Campaign Performance for Reallocation
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-04",
    "end_date": "2025-03-18",
    "dimensions": ["channel", "campaign"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 100
  }'
```

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `weekly_marketing_performance` | For context before optimization |
| `google_ads_performance` | When Google needs deeper analysis |
| `meta_ads_performance` | When Meta needs deeper analysis |
| `bid_strategy_optimization` | After budget changes, optimize bids |
| `audience_optimization` | When audience overlap is suspected |

---

## Best Practices

### For the AI Agent
1. **Always show current state first** — Context before recommendations
2. **Quantify expected impact** — Show projected profit/revenue changes
3. **Apply constraints** — Never recommend changes that violate minimums
4. **Prioritize by profit** — Not just ROAS or revenue
5. **Consider learning phase** — Don't cut campaigns too early

### For the Client
1. **Implement changes gradually** — Max 25% per week
2. **Monitor for 3-5 days** — Before making additional changes
3. **Trust ncROAS over ROAS** — For growth decisions
4. **Keep diversification** — Don't put all eggs in one basket
