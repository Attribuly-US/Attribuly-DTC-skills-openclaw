---
name: landing-page-analysis
version: 1.0.0
description: Diagnose landing-page conversion loss by analyzing stage progression, engagement quality, and traffic-source fit across landing pages.
---
# Skill: AllyClaw Landing Page Analysis

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly Landing Page Analysis is superior:**

| Capability | Generic Analytics | Attribuly | Why It Matters |
|------------|-------------------|-----------|----------------|
| **Funnel-Linked LP View** | Page metrics only | ✅ Ties LP performance to product, ATC, and purchase stages | Finds where LP traffic actually fails |
| **Marketing Context** | Limited source context | ✅ LP breakdown by channel, campaign, source, medium | Separates traffic-quality issues from LP UX issues |
| **Business Impact** | Traffic-focused only | ✅ Includes spend, purchases, and revenue | Prioritizes fixes with highest financial upside |

**Key Insight:** Not every low-converting landing page is a page problem. Attribuly helps distinguish whether the leak comes from traffic intent mismatch, weak LP content, or downstream checkout friction.

---

## When to Trigger This Skill

### Automatic Triggers
- Homepage → Product View drop-off > 20% in `funnel_analysis`.
- High-spend landing pages with low engagement and weak progression.
- Sudden CVR decline after landing page updates.

### Manual Triggers (User Commands)
- "Analyze landing page performance"
- "Which landing pages are leaking conversions?"
- "Why is this page not converting?"
- "Compare conversion by landing page"
- "Find low-performing LPs"

### Context Triggers
- After launching new creatives or offer changes.
- After redesigning key campaign landing pages.
- During periods of increasing spend but flat purchases.

---

## Skill Purpose

Provide a clear landing-page diagnostic report focused on:
1. **Leakage Detection** — Identify LPs with abnormal drop-offs.
2. **Cause Isolation** — Determine traffic-fit vs LP-experience issues.
3. **Impact Prioritization** — Rank issues by lost purchase/revenue impact.
4. **Action Plan** — Recommend fixes and validation tests.

---

## Data Sources

### Primary APIs

#### 1. Web Analytics List
**Endpoint:** `POST /{version}/api/get/web-analysis/list`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Evaluate LP progression through key funnel stages.

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | string | Yes | Start date (`YYYY-MM-DD`) |
| `end_date` | string | Yes | End date (`YYYY-MM-DD`) |
| `dimensions` | array[string] | Yes | Must include `landing_page`; recommended with traffic dimensions |

**Primary Fields Used**

| Field | Description |
|-------|-------------|
| `landing_page` | Landing page identifier/URL |
| `channel` | Channel dimension |
| `utm_campaign` | Campaign dimension |
| `utm_source` | Source dimension |
| `utm_medium` | Medium dimension |
| `homepage_view_users` | Landing page entry users |
| `product_view_users` | Product viewers from LP |
| `atc_users` | Add-to-cart users from LP path |
| `purchases` | Purchasers from LP path |
| `purchases_rate` | Overall purchase conversion rate |
| `engagement_rate` | Engagement quality signal |
| `event_per_session` | Interaction depth |
| `spend` | Cost tied to LP traffic |
| `revenue` | Revenue attributed to LP path |

#### 2. Attribution List
**Endpoint:** `POST /{version}/api/all-attribution/get-list`  
**Purpose:** Add attribution context for conversion value comparisons by landing page.

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `version` | `v2-4-2` | API version |
| `start_date` | Today - 14 days | Stable diagnostic window |
| `end_date` | Today - 1 day | Yesterday, explicitly excluding today |
| `dimensions` | `["landing_page","channel","utm_campaign"]` | Primary diagnostic split |
| `page_size` | `100` | Full LP ranking coverage |
| `model` | `linear` | Attribution model |
| `goal` | `purchase` | Primary conversion goal |

---

## Execution Steps

### Step 1: Validate Input
- Ensure `start_date <= end_date`.
- Enforce max range of 90 days.
- Ensure `dimensions` contains `landing_page`.

### Step 2: Fetch Landing Page Dataset
- Call `/api/get/web-analysis/list` with landing-page-centric dimensions.
- Continue only when `code === 1`.

### Step 3: Compute Conversion Progression
For each landing page record in the dataset, apply the following logic:

**URL Type Detection:**
- Parse the `landing_page` string.
- If the URL path contains `"/products"`, classify it as a **Product Page**.
- Otherwise, classify it as a **Standard Landing Page**.

**Progression Calculation:**
- **LP → Product View**: 
  - *Rule*: Skip this calculation for Product Pages (since the user is already on the product page).
  - *Formula (Standard LP only)*: `product_view_users / homepage_view_users`
- **Product View → ATC**: `atc_users / product_view_users`
- **ATC → Purchase**: `purchases / atc_users`
- **Overall LP CVR**: `purchases_rate`

### Step 4: Rank LP Performance
- Rank LPs by worst progression rates and highest spend.
- Flag high-cost LPs with zero purchases.

### Step 5: Isolate Root Cause
- Break down weak LPs by `channel`, `utm_campaign`, `utm_source`, `utm_medium`.
- Determine whether issue is traffic intent, LP message, or downstream checkout friction.

### Step 6: Generate Recommendations
- Provide fast fixes, structural changes, and validation experiment ideas.

---

## Key Metrics to Analyze

| Metric | Field Name | Why It Matters |
|--------|------------|----------------|
| **LP Traffic** | `homepage_view_users` | Indicates qualified LP visits |
| **LP Engagement** | `engagement_rate`, `event_per_session` | Measures interest and depth |
| **Product Progression** | `product_view_users` | Tests LP relevance/clarity |
| **Intent Progression** | `atc_users` | Tests offer and product confidence |
| **Purchase Outcome** | `purchases`, `purchases_rate` | Final conversion effectiveness |
| **Cost Pressure** | `spend` | Highlights expensive leakage |
| **Revenue Yield** | `revenue` | Quantifies business impact |

---

## Root Cause Analysis Logic

### LP Entry → Product View

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Drop-off > 20% and low engagement | Message mismatch, weak hero, poor page speed | Improve above-the-fold clarity, align ad promise to LP copy, optimize speed |
| Good traffic volume but weak product progression | LP structure distracts from product path | Simplify LP layout and strengthen CTA hierarchy |

### Product View → Add to Cart

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Drop-off > 15% | Weak pricing/value framing, low trust, unclear offer | Improve pricing communication, trust signals, and offer framing |

### Add to Cart → Purchase

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Drop-off > 15% | Checkout friction, shipping surprises, payment failures | Audit checkout UX and shipping messaging; test payment reliability |

### Source/Channel-Level Pattern

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| One source drives majority of weak LP traffic | Intent mismatch or poor audience quality | Refine targeting, placements, and keyword/source mix |

---

## Expected Output

```markdown
# Landing Page Analysis Report
Date Range: [start] to [end]

## 1) Executive Summary
- Primary issue: [LP or traffic segment]
- Severity: [Normal/Warning/Critical]
- Estimated impact: [revenue/purchases at risk]

## 2) LP Performance Ranking
| Landing Page | Visits | LP→PV | PV→ATC | ATC→Purchase | Purchases | Spend | Revenue | Status |
|-------------|-------:|------:|------:|-------------:|----------:|------:|--------:|--------|
| /lp-a | 0 | 0% | 0% | 0% | 0 | 0 | 0 | ✅ |

## 3) Root Cause Summary
- Traffic quality findings
- LP UX/content findings
- Downstream checkout findings

## 4) Recommended Actions
1. Quick win
2. Structural fix
3. Validation test
```

---

## Error Handling Specifications

- **Unauthorized (`401`)**: Stop and return authentication error.
- **General Error (`code: 0`)**: Return API `message` and mark execution failed.
- **Rate Limit (`429`)**: Retry with exponential backoff (1s, 2s, 4s; max 3 retries).
- **Server Error (`500`)**: Retry with same policy, fail after max retries.
- **Empty Results**: Return successful report with "No data for selected LP filters."
- **Division by Zero**: Set rate to `0`, mark as data-quality warning.

---

## Thresholds for Alerts

| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| LP → Product View drop-off | > 20% | > 35% |
| Product View → ATC drop-off | > 15% | > 30% |
| ATC → Purchase drop-off | > 15% | > 30% |
| `engagement_rate` | < 40% | < 20% |
| High-spend LP with `purchases = 0` | Spend > 1x avg LP spend | Spend > 2x avg LP spend |

---

## Example API Calls

### 1. Landing Page + Channel Breakdown

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/web-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-17",
    "dimensions": ["landing_page", "channel", "utm_campaign"]
  }'
```

### 2. Landing Page + Source/Medium Breakdown

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/web-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-17",
    "dimensions": ["landing_page", "utm_source", "utm_medium"]
  }'
```

---

## Dependencies

| Dependency | Type | Purpose |
|------------|------|---------|
| Attribuly Data API | External API | Fetch LP funnel and segment-level metrics |
| ApiKey | Auth credential | Authenticate API access |
| Date utilities | Runtime utility | Build windows and comparisons |
| Retry utility | Runtime utility | Handle 429/5xx resilience |

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `funnel_analysis` | Parent diagnostic signal for conversion leakage |
| `budget_optimization` | Spend concentration on weak LPs |
| `attribution_discrepancy` | LP purchases diverge from backend orders |
