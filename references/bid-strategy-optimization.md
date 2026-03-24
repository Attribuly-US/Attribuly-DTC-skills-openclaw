---
name: bid-strategy-optimization
version: 1.0.0
description: Set accurate bid targets for Google and Meta Ads. Use Attribuly's first-party Shopify and WooCommerce data to optimize ecommerce CPA and ROAS.
---
# Skill: AllyClaw Ecommerce Bid Strategy Optimizer

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly-powered bid optimization is superior:**

| Metric | Native Ads Managers | Attribuly | Why It Matters |
|--------|---------------------|-----------|----------------|
| **True CPA** | ⚠️ Inflated conversions | ✅ First-party data | Set targets based on REAL acquisition cost |
| **ncCPA** | ❌ Not available | ✅ Calculated | Cost to acquire NEW customers only |
| **True ROAS** | ⚠️ Over-attributed | ✅ `roas` | Set ROAS targets based on truth |
| **Profit-Based Bidding** | ❌ Not available | ✅ `profit` | Optimize for profit, not vanity metrics |
| **LTV-Informed Targets** | ❌ Not available | ✅ `ltv` | Set targets based on customer lifetime value |

**Key Insight:** Most DTC brands set bid targets based on platform-reported metrics, which are inflated 30-60%. This leads to overbidding and wasted spend. Attribuly reveals the TRUE CPA and ROAS, enabling accurate bid targets that maximize profit.

---

## When to Trigger This Skill

### Automatic Triggers
- **After Budget Changes**: When budget optimization skill makes recommendations
- **CPA Target Missed**: When actual CPA > target CPA for 5+ days
- **ROAS Target Missed**: When actual ROAS < target ROAS for 5+ days
- **Learning Phase Complete**: When campaigns exit learning phase
- **Significant Performance Shift**: When CPA or ROAS changes >20%

### Manual Triggers (User Commands)
- "Optimize bids"
- "Bid strategy recommendations"
- "What should my target CPA be?"
- "What ROAS target should I set?"
- "Bidding strategy"
- "tCPA vs tROAS"
- "Should I use manual or automated bidding?"
- "My CPA is too high"

### Context Triggers
- After budget reallocation
- When launching new campaigns
- When scaling existing campaigns
- When profitability is declining despite stable ROAS

---

## Skill Purpose

Provide **data-driven bid strategy recommendations** to:
1. **Set Accurate Targets** based on first-party data (not platform inflation)
2. **Choose Optimal Bid Strategy** (tCPA, tROAS, Max Conversions, etc.)
3. **Maximize Profit** not just conversions or revenue
4. **Account for LTV** in target calculations

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

#### 2. Get Campaign Performance
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Get campaign-level performance for bid analysis.

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

#### 3. Get Historical Performance (for trend analysis)
**Endpoint:** `POST /{version}/api/all-attribution/get-list`
**Purpose:** Get historical data for target calculation.

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

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Linear attribution |
| `goal` | Dynamic from Settings API | Use `checkout_completed` event type |
| `lookback_period` | 30 days | Enough data for stable targets |
| `min_conversions` | 30 | Minimum conversions for reliable targets |

---

## Key Metrics for Bid Optimization

### 🌟 Attribuly-Exclusive Metrics (PRIMARY)
| Metric | Field Name | Bid Use |
|--------|------------|---------|
| **True CPA** | `cpa` | Set accurate tCPA targets |
| **ncCPA** | `spend / new_order_conversions` | Cost to acquire NEW customers (purchases) |
| **True ROAS** | `roas` | Set accurate tROAS targets |
| **ncROAS** | `new_order_roas` | ROAS for new customers only |
| **ncRevenue** | `new_order_conversion_value` | Revenue from new customers |
| **Profit** | `profit` | Calculate profitable bid ceiling |
| **Margin** | `margin` | Ensure bids maintain profitability |
| **LTV** | `ltv` | Inform maximum acceptable CPA |
| **CPL** | `spend / new_lead_conversions` | Cost per Lead (email subscriber) |

### Standard Metrics
| Metric | Field Name | Bid Use |
|--------|------------|---------|
| **Conversions** | `conversions` | Volume for target calculation |
| **Revenue** | `conversion_value` | Revenue for ROAS calculation |
| **Spend** | `spend` | Current spend level |
| **CVR** | `cvr` | Conversion rate for bid modeling |

### Calculated Metrics
| Metric | Formula | Purpose |
|--------|---------|---------|
| **Break-Even CPA** | `AOV × Margin` | Maximum CPA before losing money |
| **Target CPA** | `Break-Even CPA × 0.7` | Target with 30% profit buffer |
| **LTV-Based Max CPA** | `LTV × Margin × 0.5` | Max CPA considering lifetime value |
| **Platform Inflation** | `(Platform CPA - Attribuly CPA) / Platform CPA` | How much platform over-reports |

---

## Bid Strategy Framework

### Step 1: Calculate True Metrics
```
For each campaign:
- True CPA = spend / conversions (Attribuly)
- ncCPA = spend / new_lead_conversions
- True ROAS = conversion_value / spend (Attribuly)
- Platform Inflation = (Platform CPA - True CPA) / Platform CPA
```

### Step 2: Calculate Target Thresholds
```
Break-Even CPA = AOV × Gross Margin %
Target CPA = Break-Even CPA × 0.7 (30% profit buffer)
Max CPA = LTV × Gross Margin % × 0.5 (50% of LTV margin)

Break-Even ROAS = 1 / Gross Margin %
Target ROAS = Break-Even ROAS × 1.3 (30% profit buffer)
```

### Step 3: Determine Optimal Bid Strategy

| Scenario | Recommended Strategy | Rationale |
|----------|---------------------|-----------|
| **New Campaign (<30 conversions)** | Maximize Conversions | Build data first |
| **Scaling Campaign (30-100 conversions)** | tCPA or tROAS | Optimize efficiency |
| **Mature Campaign (>100 conversions)** | tROAS with profit target | Maximize profit |
| **High AOV (>$200)** | tROAS | Revenue variance matters |
| **Low AOV (<$50)** | tCPA | Volume matters more |
| **High LTV Products** | Higher tCPA | Can afford higher acquisition cost |

### Step 4: Set Platform-Adjusted Targets
```
Since platforms over-attribute:
Platform tCPA Target = True Target CPA × (1 + Platform Inflation)
Platform tROAS Target = True Target ROAS × (1 - Platform Inflation)

Example:
- True Target CPA = $30
- Platform Inflation = 40%
- Platform tCPA Target = $30 × 1.4 = $42
```

---

## Bid Strategy Recommendations by Platform

### Google Ads

| Strategy | When to Use | Attribuly-Informed Target |
|----------|-------------|---------------------------|
| **Maximize Conversions** | New campaigns, <30 conversions | No target needed |
| **Target CPA** | Established campaigns, consistent CPA | True CPA × (1 + inflation) |
| **Target ROAS** | High AOV, revenue focus | True ROAS × (1 - inflation) |
| **Maximize Conversion Value** | Scaling, variable AOV | No target, monitor ROAS |
| **Manual CPC** | Brand campaigns, full control | Based on target CPA / CVR |

**Google-Specific Considerations:**
- PMax: Use tROAS, set based on Attribuly ROAS (not Google-reported)
- Search: tCPA works well for consistent products
- Shopping: tROAS preferred due to product mix

### Meta Ads

| Strategy | When to Use | Attribuly-Informed Target |
|----------|-------------|---------------------------|
| **Lowest Cost** | New campaigns, testing | No target, monitor CPA |
| **Cost Cap** | Established campaigns | True CPA × (1 + inflation) |
| **Bid Cap** | Strict CPA control | True CPA × (1 + inflation) |
| **ROAS Goal** | ASC, Catalog campaigns | True ROAS × (1 - inflation) |
| **Highest Value** | High AOV, LTV focus | No target, monitor ROAS |

**Meta-Specific Considerations:**
- ASC: Use ROAS Goal, set based on Attribuly ncROAS
- Prospecting: Cost Cap preferred for new customer acquisition
- Retargeting: Lowest Cost often sufficient (high intent)

---

## Target Calculation Examples

### Example 1: Calculate Target CPA

**Given:**
- AOV = $80
- Gross Margin = 50%
- LTV = $200
- Platform Inflation = 35%

**Calculation:**
```
Break-Even CPA = $80 × 50% = $40
Target CPA (with 30% buffer) = $40 × 0.7 = $28
LTV-Based Max CPA = $200 × 50% × 0.5 = $50

Recommended True Target CPA = $28 (conservative)
Platform tCPA Target = $28 × 1.35 = $37.80 ≈ $38
```

### Example 2: Calculate Target ROAS

**Given:**
- Gross Margin = 40%
- Platform Inflation = 40%

**Calculation:**
```
Break-Even ROAS = 1 / 40% = 2.5x
Target ROAS (with 30% buffer) = 2.5 × 1.3 = 3.25x

Recommended True Target ROAS = 3.25x
Platform tROAS Target = 3.25 × (1 - 0.4) = 1.95x ≈ 2.0x
```

---

## Output Format

```markdown
# 🎯 Bid Strategy Optimization
**Analysis Period:** [Start Date] to [End Date]
**Campaigns Analyzed:** XX
**Total Spend:** $XX,XXX

---

## 📊 Current Bid Performance

### By Channel

| Channel | Strategy | Current Target | Actual CPA | Actual ROAS | Status |
|---------|----------|----------------|------------|-------------|--------|
| Google | tCPA | $XX | $XX.XX | X.XX | ✅/🟡/🔴 |
| Meta | Cost Cap | $XX | $XX.XX | X.XX | ✅/🟡/🔴 |

### Platform vs Attribuly Gap

| Channel | Platform CPA | Attribuly CPA | Inflation | Platform ROAS | Attribuly ROAS |
|---------|--------------|---------------|-----------|---------------|----------------|
| Google | $XX.XX | $XX.XX | XX% | X.XX | X.XX |
| Meta | $XX.XX | $XX.XX | XX% | X.XX | X.XX |

---

## 🧮 Target Calculations

### Your Business Metrics
| Metric | Value | Source |
|--------|-------|--------|
| Average Order Value (AOV) | $XX | Attribuly |
| Gross Margin | XX% | Client Input |
| Customer LTV | $XXX | Attribuly |
| Platform Inflation (Avg) | XX% | Calculated |

### Calculated Targets

| Metric | Break-Even | Target (30% buffer) | LTV-Based Max |
|--------|------------|---------------------|---------------|
| CPA | $XX | $XX | $XX |
| ROAS | X.Xx | X.Xx | N/A |

### Platform-Adjusted Targets

| Platform | True Target CPA | Platform tCPA | True Target ROAS | Platform tROAS |
|----------|-----------------|---------------|------------------|----------------|
| Google | $XX | $XX | X.Xx | X.Xx |
| Meta | $XX | $XX | X.Xx | X.Xx |

---

## 🎯 Bid Strategy Recommendations

### Google Ads

| Campaign | Current Strategy | Recommended | Current Target | New Target | Reason |
|----------|------------------|-------------|----------------|------------|--------|
| [Campaign A] | Max Conversions | tCPA | N/A | $XX | 50+ conversions, ready for efficiency |
| [Campaign B] | tCPA | tROAS | $XX | X.Xx | High AOV, revenue variance |

### Meta Ads

| Campaign | Current Strategy | Recommended | Current Target | New Target | Reason |
|----------|------------------|-------------|----------------|------------|--------|
| [Campaign C] | Lowest Cost | Cost Cap | N/A | $XX | CPA too high, need control |
| [Campaign D] | Cost Cap | ROAS Goal | $XX | X.Xx | ASC, optimize for value |

---

## 💡 Key Insights

### 1. Platform Inflation Analysis
- **Google** is over-reporting by XX% — adjust targets accordingly
- **Meta** is over-reporting by XX% — adjust targets accordingly

### 2. Profitability Check
- Current blended CPA: $XX (Target: $XX) — [On Track / Over / Under]
- Current blended ROAS: X.Xx (Target: X.Xx) — [On Track / Over / Under]
- Estimated profit at current bids: $X,XXX

### 3. LTV Consideration
- Average LTV: $XXX
- LTV:CAC Ratio: X.Xx (Target: >3.0)
- Can afford higher CPA for high-LTV products

---

## 📋 Action Items

### Immediate (This Week)
1. **Update [Campaign A] to tCPA** with target $XX
2. **Reduce [Campaign B] tCPA** from $XX to $XX
3. **Switch [Campaign C] to Cost Cap** at $XX

### After 7 Days
4. **Review performance** and adjust targets ±10%
5. **Consider tROAS** for campaigns with 100+ conversions

### Monitor
6. **Track Attribuly CPA** vs Platform CPA weekly
7. **Recalculate targets** if AOV or margin changes

---

## ⚠️ Warnings

- 🟡 [Campaign X] has <30 conversions — targets may be unstable
- 🟡 [Campaign Y] in learning phase — wait before adjusting
- 🔴 [Campaign Z] CPA 50% over target — consider pausing
```

---

## Thresholds for Alerts

### CPA Thresholds
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| CPA > Target by 10-20% | 🟡 Warning | Monitor, minor adjustment |
| CPA > Target by 20-40% | 🟠 High | Reduce target or pause |
| CPA > Target by >40% | 🔴 Critical | Pause, investigate |
| CPA > Break-Even | 🔴 Critical | Losing money, immediate action |

### ROAS Thresholds
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| ROAS < Target by 10-20% | 🟡 Warning | Monitor, minor adjustment |
| ROAS < Target by 20-40% | 🟠 High | Increase target or pause |
| ROAS < Target by >40% | 🔴 Critical | Pause, investigate |
| ROAS < Break-Even | 🔴 Critical | Losing money, immediate action |

### Learning Phase
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| <30 conversions | 🟡 Warning | Don't set aggressive targets |
| In learning phase | 🟡 Warning | Don't make changes |
| Learning limited | 🟠 High | Increase budget or broaden targeting |

---

## Example API Calls

### 1. Get Campaign Performance (Last 30 Days)
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-02-17",
    "end_date": "2025-03-18",
    "dimensions": ["channel", "campaign"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 100
  }'
```

### 2. Get Channel Summary
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list-sum" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-02-17",
    "end_date": "2025-03-18",
    "dimensions": ["channel"],
    "model": "linear",
    "goal": "purchase"
  }'
```

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `budget_optimization` | Before bid changes, ensure budget is optimized |
| `audience_optimization` | If bid changes don't improve performance |
| `google_ads_performance` | For Google-specific bid analysis |
| `meta_ads_performance` | For Meta-specific bid analysis |
| `weekly_marketing_performance` | For overall context |

---

## Best Practices

### For the AI Agent
1. **Always calculate platform inflation** — Critical for accurate targets
2. **Use 30-day data minimum** — For stable target calculation
3. **Account for learning phase** — Don't recommend changes too early
4. **Show break-even clearly** — Client must understand the floor
5. **Recommend gradual changes** — Max 15-20% target change at once

### For the Client
1. **Trust Attribuly targets** — Not platform-reported metrics
2. **Wait 7 days after changes** — Before evaluating
3. **Don't chase daily fluctuations** — Look at weekly trends
4. **Consider LTV** — Higher CPA may be acceptable for high-LTV products
5. **Monitor profit** — Not just CPA or ROAS
