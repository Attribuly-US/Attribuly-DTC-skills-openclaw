---
name: funnel-analysis
version: 1.0.1
description: Diagnose conversion drop-offs across Homepage → Product View → Add to Cart → Purchase, then quantify impact by channel, campaign, and landing page.
---
# Skill: AllyClaw Funnel Analysis

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly Funnel Analysis is superior to generic analytics views:**

| Capability | Generic Analytics | Attribuly | Why It Matters |
|------------|-------------------|-----------|----------------|
| **Journey View** | Partial/siloed | ✅ Unified funnel stages | Detect exact leakage point |
| **Marketing Context** | Limited attribution context | ✅ UTM + channel + landing page context | Connect drop-off to traffic quality |
| **Business Impact** | User events only | ✅ Includes spend and revenue | Prioritize high-impact fixes first |

**Key Insight:** A flat overall CVR can hide severe stage-level leakage. This skill isolates where leakage occurs and which traffic segments create it.

---

## When to Trigger This Skill

### Automatic Triggers
- Global CVR drops > 15% week-over-week.
- Any paid channel CVR drops > 15% week-over-week.
- Triggered after `weekly_marketing_performance` flags conversion instability.

### Manual Triggers (User Commands)
- "Why did CVR drop?"
- "Where are users dropping off?"
- "Run funnel analysis"
- "Analyze checkout drop-off"
- "Compare funnel by channel"

### Context Triggers
- After landing page redesign or checkout changes.
- During performance incidents with rising spend but weak purchases.
- When traffic quality concerns appear (high sessions, low purchase progression).

---

## Skill Purpose

Deliver a practical diagnostic report with:
1. **Leakage Detection** — Identify the weakest stage.
2. **Segment Isolation** — Find channels/campaigns/pages driving the leak.
3. **Revenue Prioritization** — Focus fixes where lost revenue is highest.
4. **Action Plan** — Produce concrete remediation steps.

---

## Data Sources

### Primary API

#### 1. Web Analytics Funnel
**Endpoint:** `POST /{version}/api/get/web-analysis/list`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Fetch funnel metrics with dimension breakdown.

**Required Request Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | string | Yes | Start date (`YYYY-MM-DD`) |
| `end_date` | string | Yes | End date (`YYYY-MM-DD`) |
| `dimensions` | array[string] | Yes | Breakdown keys (e.g., `channel`, `utm_campaign`, `landing_page`) |

**Response Data Points Used**

| Field | Description |
|-------|-------------|
| `channel` | Traffic/acquisition channel |
| `utm_campaign` | Campaign identifier |
| `utm_source` | Traffic source |
| `utm_medium` | Medium classification |
| `landing_page` | Entry page URL/path |
| `spend` | Ad spend |
| `unique_users` | Unique users |
| `returning_users` | Returning users |
| `total_sessions` | Total sessions |
| `event_per_session` | Engagement depth proxy |
| `engagement_rate` | Engagement quality |
| `homepage_view_users` | Stage 1 users |
| `product_view_users` | Stage 2 users |
| `atc_users` | Stage 3 users |
| `purchases` | Stage 4 users |
| `purchases_rate` | End-to-end conversion rate |
| `revenue` | Attributed revenue |

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `version` | `v2-4-2` | API version |
| `start_date` | Today - 7 days | Diagnostic default window |
| `end_date` | Today - 1 day | Yesterday, explicitly excluding today |
| `dimensions` | `["channel","utm_campaign","landing_page"]` | Root-cause breakdown |
| `page_size` | `99` | Align with web analytics sample size |

---

## Execution Steps

### Step 1: Validate Inputs
- Confirm `start_date <= end_date`.
- Enforce max window of 90 days.
- Validate `dimensions` is non-empty.

### Step 2: Fetch Funnel Dataset
- Call `/api/get/web-analysis/list` with date range + dimensions.
- Confirm response `code === 1` before processing.

### Step 3: Calculate Stage Conversion
- Homepage → Product View = `product_view_users / homepage_view_users`
- Product View → Add to Cart = `atc_users / product_view_users`
- Add to Cart → Purchase = `purchases / atc_users`
- Overall CVR = `purchases_rate`

### Step 4: Detect Leakage
- Compute stage drop-off percentages.
- Compare against baseline and thresholds.
- Mark bottleneck stage by largest abnormal drop.

### Step 5: Attribute Cause
- Rank worst segments by `channel`, `utm_campaign`, `landing_page`.
- Quantify impact using `spend`, `revenue`, and purchase gap.

### Step 6: Generate Recommendations
- Create prioritized actions by severity and revenue impact.
- Add related-skill escalation when needed.

---

## Key Metrics to Analyze

| Metric | Field Name | Why It Matters |
|--------|------------|----------------|
| **Homepage Traffic** | `homepage_view_users` | Top-of-funnel volume quality |
| **Product Interest** | `product_view_users` | Merchandising relevance |
| **Purchase Intent** | `atc_users` | Offer + PDP effectiveness |
| **Completed Orders** | `purchases` | Final conversion outcome |
| **End-to-End CVR** | `purchases_rate` | Overall efficiency |
| **Revenue Yield** | `revenue` | Commercial impact |
| **Spend Load** | `spend` | Cost pressure |
| **Engagement Quality** | `engagement_rate`, `event_per_session` | Traffic fit signal |

---

## Root Cause Analysis Logic

### Stage 1: Homepage → Product View

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Drop-off > 20% | Message mismatch, weak page relevance, low-quality traffic | Trigger `landing_page_analysis`; review source/medium alignment |
| Low `engagement_rate` | Poor audience fit | Refine targeting and channel mix |

### Stage 2: Product View → Add to Cart

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Drop-off > 15% | Pricing resistance, weak PDP copy, stock issues | Audit pricing, inventory, product content |

### Stage 3: Add to Cart → Purchase

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Drop-off > 15% | Checkout friction, shipping surprise, payment failures | Audit checkout UX, shipping policy, payment logs |

---

## Expected Output

```markdown
# Funnel Analysis Report
Date Range: [start] to [end]

## 1) Health Summary
- Overall CVR: X.XX%
- Primary Bottleneck: [Stage]
- Severity: [Normal/Warning/Critical]

## 2) Stage Breakdown
| Stage | Users | Drop-off | CVR to Next | Status |
|------|------:|---------:|------------:|--------|
| Homepage | 0 | - | - | - |
| Product View | 0 | 0% | 0% | ✅ |
| Add to Cart | 0 | 0% | 0% | ✅ |
| Purchase | 0 | 0% | 0% | ✅ |

## 3) Worst Segments
- Channel: ...
- Campaign: ...
- Landing Page: ...

## 4) Recommendations
1. ...
2. ...
3. ...
```

---

## Error Handling Specifications

- **Unauthorized (`401`)**: Stop execution and return authentication failure.
- **General Error (`code: 0`)**: Return API message and mark run failed.
- **Rate Limit (`429`)**: Retry with exponential backoff (1s, 2s, 4s; max 3 retries).
- **Server Error (`500`)**: Retry with same policy; fail if retries exhausted.
- **Empty Dataset**: Return successful run with "No funnel data for selected filters."
- **Division by Zero**: Return stage CVR as `0` and flag data-quality warning.

---

## Thresholds for Alerts

| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| Homepage → Product drop-off | > 15% vs baseline | > 30% vs baseline |
| Product → ATC drop-off | > 15% vs baseline | > 30% vs baseline |
| ATC → Purchase drop-off | > 15% vs baseline | > 30% vs baseline |
| Overall `purchases_rate` | < 2% | < 1% |
| `engagement_rate` | < 40% | < 20% |

---

## Example API Calls

### 1. Funnel by Channel + Landing Page

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/web-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-17",
    "dimensions": ["channel", "landing_page"]
  }'
```

### 2. Funnel by Campaign

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/web-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-17",
    "dimensions": ["utm_campaign", "utm_source", "utm_medium"]
  }'
```

---

## Dependencies

| Dependency | Type | Purpose |
|------------|------|---------|
| Attribuly Data API | External API | Fetch funnel and dimension metrics |
| ApiKey | Auth credential | Authenticate all requests |
| Date utilities | Runtime utility | Build rolling windows and baseline periods |
| Retry utility | Runtime utility | Handle `429` and transient `5xx` safely |

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `landing_page_analysis` | Critical top-of-funnel leakage |
| `attribution_discrepancy` | Mismatch between purchases and backend orders |
| `budget_optimization` | High spend concentrated in low-converting segments |
