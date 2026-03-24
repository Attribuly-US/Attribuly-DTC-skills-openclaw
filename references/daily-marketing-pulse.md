---
name: daily-marketing-pulse
version: 1.0.0
description: Daily marketing snapshot for DTC brands. Monitor Shopify and WooCommerce spend, profit, and Attribuly incrementality metrics in real-time.
---
# Skill: AllyClaw Daily Marketing Pulse for Ecommerce

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly Daily Pulse is superior to native platform dashboards:**

| Metric | Native Ads Managers | Attribuly | Why It Matters |
|--------|---------------------|-----------|----------------|
| **ncROAS** | ❌ Not available | ✅ `new_order_roas` | See TRUE incremental value daily |
| **ncPurchase** | ❌ Not available | ✅ `new_lead_conversions` | Track new customer acquisition daily |
| **Profit** | ❌ Not available | ✅ `profit` | Know if you're profitable TODAY |
| **Margin** | ❌ Not available | ✅ `margin` | Daily profitability check |
| **LTV** | ❌ Not available | ✅ `ltv` | Customer quality indicator |
| **Cross-Channel View** | ⚠️ Siloed dashboards | ✅ Unified | One view of all channels |
| **True ROAS** | ⚠️ Inflated 30-60% | ✅ First-party data | Accurate daily performance |

**Key Insight:** Daily monitoring with Attribuly catches problems BEFORE they become expensive. A single day of poor ncROAS can indicate creative fatigue, audience saturation, or budget waste that platforms won't show you.

---

## When to Trigger This Skill

### Automatic Triggers
- **Scheduled**: Every day at 09:00 AM (client's timezone)
- **Weekend Summary**: Saturday at 10:00 AM (covers Fri-Sat)

### Manual Triggers (User Commands)
- "Daily update"
- "How did we do yesterday?"
- "Daily pulse"
- "Quick performance check"
- "What happened today?"
- "Daily marketing summary"
- "Morning report"

### Context Triggers
- When user asks about recent performance
- When user wants a quick status check
- After launching new campaigns (monitor learning phase)
- During high-spend periods (sales, promotions)

---

## Skill Purpose

Provide a **quick, actionable daily snapshot** of marketing performance. Focus on:
1. **Anomaly Detection** — Flag anything unusual immediately
2. **Spend Pacing** — Are we on track for daily/weekly budget?
3. **Quick Wins** — What's working that we can scale?
4. **Red Flags** — What needs immediate attention?

**Design Principle:** Daily Pulse should be **scannable in 30 seconds**. Save deep analysis for weekly reports.

---

## Data Sources

### Primary APIs

#### 1. Get Yesterday's Totals
**Endpoint:** `POST /{version}/api/all-attribution/get-list-sum`
**Purpose:** Get aggregated totals for yesterday.

```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimensions": ["channel"],
  "model": "linear",
  "goal": "purchase"
}
```

#### 2. Get Channel Breakdown
**Endpoint:** `POST /{version}/api/all-attribution/get-list`
**Purpose:** Get breakdown by channel for yesterday.

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

#### 3. Get Campaign-Level Data (for anomalies)
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`
**Purpose:** Drill down to campaign level when anomalies detected.

```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "dimensions": ["channel", "campaign"],
  "model": "linear",
  "goal": "purchase",
  "orders": [{"column": "spend", "order": "desc"}],
  "page": 1,
  "page_size": 50
}
```

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Linear attribution |
| `goal` | `purchase` | Focus on purchase conversions |
| `dimensions` | `["channel"]` for summary | |
| `page_size` | `50` | Smaller for daily (faster) |

---

## Date Range Calculation

### Yesterday (Primary)
```
yesterday_start = TODAY - 1 day
yesterday_end = TODAY - 1 day
```

### Same Day Last Week (Comparison)
```
last_week_same_day_start = TODAY - 8 days
last_week_same_day_end = TODAY - 8 days
```

### Week-to-Date (Pacing)
```
wtd_start = Most recent Monday
wtd_end = TODAY - 1 day
```

**Example (if TODAY = 2025-03-18, Tuesday):**
- Yesterday: 2025-03-17 (Monday)
- Same Day Last Week: 2025-03-10 (Monday)
- Week-to-Date: 2025-03-17 to 2025-03-17 (just Monday so far)

---

## Execution Steps

### Step 1: Fetch Yesterday's Totals
Call `get-list-sum` API for yesterday.

### Step 2: Fetch Same Day Last Week
Call `get-list-sum` API for the same day last week (for DoD comparison).

### Step 3: Calculate Day-over-Day Changes
```
change_percent = ((yesterday_value - last_week_value) / last_week_value) * 100
```

### Step 4: Check for Anomalies
Flag any metric with:
- **Critical**: |change_percent| > 30%
- **Warning**: |change_percent| > 20%
- **Notable**: |change_percent| > 15%

### Step 5: Fetch Channel Breakdown
Call `get-list` API with `dimensions: ["channel"]` for yesterday.

### Step 6: Identify Top/Bottom Performers
- Top 3 channels by revenue
- Bottom 3 channels by ROAS (if spending)
- Any channel with $0 conversions but >$100 spend

### Step 7: Check Spend Pacing
Compare actual spend vs. expected daily spend (weekly budget / 7).

### Step 8: Generate Quick Insights
Apply anomaly detection logic and generate 3-5 bullet points.

---

## Key Metrics to Analyze

### 🌟 Attribuly-Exclusive Metrics (PRIMARY)
| Metric | Field Name | Daily Focus |
|--------|------------|-------------|
| **ncROAS** | `new_order_roas` | Is today's spend acquiring NEW customers? |
| **ncRevenue** | `new_order_conversion_value` | Revenue from new customers today |
| **Profit** | `profit` | Are we profitable TODAY? |
| **Margin** | `margin` | Daily margin health check |
| **Leads** | `new_lead_conversions` | Lead/email subscriber count today |

### Standard Metrics
| Metric | Field Name | Daily Focus |
|--------|------------|-------------|
| **Revenue** | `conversion_value` | Total revenue yesterday |
| **Conversions** | `conversions` | Total purchases yesterday |
| **Spend** | `spend` | Total ad spend yesterday |
| **ROAS** | `roas` | Overall efficiency |
| **CPA** | `cpa` | Cost per acquisition |

### Pacing Metrics
| Metric | Calculation | Purpose |
|--------|-------------|---------|
| **Daily Spend vs Target** | `actual_spend / (weekly_budget / 7)` | Budget pacing |
| **Daily Revenue vs Target** | `actual_revenue / (weekly_target / 7)` | Revenue pacing |
| **Conversion Pace** | `conversions / daily_target` | Conversion pacing |

---

## Anomaly Detection Logic

### Spend Anomalies
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| Spend > 150% of daily target | 🔴 Critical | Check for runaway campaigns |
| Spend < 50% of daily target | 🟡 Warning | Check for paused campaigns or delivery issues |
| Single campaign > 40% of total spend | 🟡 Warning | Concentration risk |

### Performance Anomalies
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| ROAS < 50% of target | 🔴 Critical | Immediate investigation |
| ncROAS < 1.0 | 🔴 Critical | Not acquiring new customers profitably |
| Profit < $0 | 🔴 Critical | Losing money |
| CPA > 150% of target | 🟡 Warning | Efficiency declining |
| CTR dropped >25% vs last week | 🟡 Warning | Creative fatigue likely |

### Incrementality Anomalies (Attribuly Exclusive)
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| ncROAS / ROAS < 30% | 🔴 Critical | Cannibalizing existing customers |
| ncRevenue = 0 but revenue > 0 | 🔴 Critical | Only repeat customers converting |
| Margin < 10% | 🟡 Warning | Low profitability |
| LTV < $30 | 🟡 Warning | Acquiring low-quality customers |

### Zero-Conversion Alerts
| Condition | Alert Level | Action |
|-----------|-------------|--------|
| Channel with >$200 spend, 0 conversions | 🔴 Critical | Investigate immediately |
| Campaign with >$100 spend, 0 conversions | 🟡 Warning | Check targeting/creative |

---

## Output Format

```markdown
# 📊 Daily Marketing Pulse
**Date:** [Yesterday's Date] ([Day of Week])
**Compared to:** Same day last week ([Date])

---

## ⚡ Quick Status

| Metric | Yesterday | vs Last Week | Status |
|--------|-----------|--------------|--------|
| Revenue | $XX,XXX | +X.X% | ✅/🟡/🔴 |
| Spend | $X,XXX | +X.X% | ✅/🟡/🔴 |
| ROAS | X.XX | +X.X% | ✅/🟡/🔴 |
| Conversions | XXX | +X.X% | ✅/🟡/🔴 |

### 🌟 Attribuly Insights

| Metric | Yesterday | vs Last Week | Status |
|--------|-----------|--------------|--------|
| **ncROAS** | X.XX | +X.X% | ✅/🟡/🔴 |
| **ncRevenue** | $X,XXX | +X.X% | ✅/🟡/🔴 |
| **Profit** | $X,XXX | +X.X% | ✅/🟡/🔴 |
| **Margin** | XX% | +X.X% | ✅/🟡/🔴 |

**Incrementality:** X.X% of revenue from NEW customers
**Profitability:** [✅ Profitable / 🟡 Break-even / 🔴 Unprofitable]

---

## 🚨 Alerts (if any)

### 🔴 Critical
- [Alert description with specific numbers]

### 🟡 Warning
- [Alert description with specific numbers]

---

## 📈 Channel Snapshot

| Channel | Revenue | ROAS | ncROAS | Profit | Status |
|---------|---------|------|--------|--------|--------|
| Meta | $X,XXX | X.XX | X.XX | $XXX | ✅/🟡/🔴 |
| Google | $X,XXX | X.XX | X.XX | $XXX | ✅/🟡/🔴 |
| TikTok | $X,XXX | X.XX | X.XX | $XXX | ✅/🟡/🔴 |

**Best Performer:** [Channel] with X.XX ncROAS
**Needs Attention:** [Channel] — [reason]

---

## 💰 Spend Pacing

| Metric | Actual | Target | Pace |
|--------|--------|--------|------|
| Daily Spend | $X,XXX | $X,XXX | XX% |
| WTD Spend | $X,XXX | $X,XXX | XX% |
| WTD Revenue | $XX,XXX | $XX,XXX | XX% |

**Pacing Status:** [On Track / Under-pacing / Over-pacing]

---

## 🎯 Today's Focus

1. **[Priority Action]** — [Brief reason]
2. **[Secondary Action]** — [Brief reason]
3. **[Monitor]** — [What to watch]

---

## 📋 Quick Wins

- ✅ [Campaign/Channel] performing well — consider scaling
- ✅ [Positive trend to continue]

## ⚠️ Watch List

- 👀 [Item to monitor]
- 👀 [Potential issue developing]
```

---

## Thresholds for Daily Alerts

### Standard Metrics
| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| Revenue Change (vs same day LW) | -20% to -30% | < -30% |
| ROAS Change | -20% to -30% | < -30% |
| CPA Change | +20% to +30% | > +30% |
| Spend vs Daily Target | <70% or >130% | <50% or >150% |
| Zero conversions with spend | >$100 | >$200 |

### 🌟 Attribuly-Exclusive Thresholds
| Metric | Warning (🟡) | Critical (🔴) | Why It Matters |
|--------|--------------|---------------|----------------|
| **ncROAS** | < 1.5 | < 1.0 | Not acquiring new customers profitably |
| **Incrementality (ncROAS/ROAS)** | < 50% | < 30% | Cannibalizing existing customers |
| **Profit** | < $0 | < -$200 | Losing money TODAY |
| **Margin** | < 15% | < 10% | Low profitability |
| **ncRevenue** | < 50% of yesterday | < 30% of yesterday | New customer acquisition dropping |

---

## Example API Calls

### 1. Get Yesterday's Totals
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list-sum" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-17",
    "end_date": "2025-03-17",
    "dimensions": ["channel"],
    "model": "linear",
    "goal": "purchase"
  }'
```

### 2. Get Same Day Last Week
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list-sum" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-10",
    "end_date": "2025-03-10",
    "dimensions": ["channel"],
    "model": "linear",
    "goal": "purchase"
  }'
```

### 3. Get Channel Breakdown (Yesterday)
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-17",
    "end_date": "2025-03-17",
    "dimensions": ["channel"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "conversion_value", "order": "desc"}],
    "page": 1,
    "page_size": 50
  }'
```

### 4. Get Campaign-Level (for anomaly investigation)
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2025-03-17",
    "end_date": "2025-03-17",
    "dimensions": ["channel", "campaign"],
    "model": "linear",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 50
  }'
```

---

## Comparison: Daily Pulse vs Weekly Report

| Aspect | Daily Pulse | Weekly Report |
|--------|-------------|---------------|
| **Depth** | Surface-level, quick scan | Deep analysis |
| **Time to Read** | 30 seconds | 5-10 minutes |
| **Comparison** | vs Same Day Last Week | vs Previous Week |
| **Focus** | Anomalies & Pacing | Trends & Strategy |
| **Actions** | Immediate fixes | Strategic changes |
| **Drill-Down** | Only if anomaly detected | Always included |
| **Recommendations** | 2-3 quick actions | Comprehensive plan |

---

## When to Escalate to Deeper Analysis

Trigger `weekly_marketing_performance` or channel-specific skills when:

1. **3+ consecutive days** of declining ROAS
2. **Critical alert** that persists for 2+ days
3. **Spend anomaly** > 50% of target
4. **ncROAS < 1.0** for 2+ consecutive days
5. **Profit negative** for 2+ consecutive days

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `weekly_marketing_performance` | When daily anomalies persist 3+ days |
| `google_ads_performance` | When Google shows critical alert |
| `meta_ads_performance` | When Meta shows critical alert |
| `creative_fatigue_detector` | When CTR drops >25% |
| `budget_optimization` | When spend pacing is off by >30% |

---

## Daily Pulse Best Practices

### For the AI Agent
1. **Be concise** — Daily pulse should be scannable
2. **Lead with alerts** — Critical issues first
3. **Use emojis** — Visual status indicators (✅🟡🔴)
4. **Compare to same day last week** — Day-of-week matters for DTC
5. **Always show ncROAS** — The most important Attribuly metric
6. **Include profit** — Revenue without profit is vanity

### For the Client
1. **Check daily pulse every morning** — Catch issues early
2. **Act on critical alerts immediately** — Don't wait for weekly
3. **Use pacing to adjust budgets** — Stay on track
4. **Trust Attribuly over platform dashboards** — First-party truth
