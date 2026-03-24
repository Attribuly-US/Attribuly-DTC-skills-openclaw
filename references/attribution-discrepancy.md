---
name: attribution-discrepancy
version: 1.0.0
description: Identifies and diagnoses reporting discrepancies between ad platform metrics (Meta/Google), Attribuly's unified attribution, and backend store data (Shopify/WooCommerce).
---
# Skill: AllyClaw Attribution Discrepancy Analysis

## Skill Metadata

| Field           | Value                                                                                                                                                                      |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Skill ID**    | `attribution_discrepancy`                                                                                                                                                  |
| **Name**        | AllyClaw Attribution Discrepancy Analysis                                                                                                                                  |
| **Description** | Identifies and diagnoses reporting discrepancies between ad platform metrics (Meta/Google), Attribuly's unified attribution, and backend store data (Shopify/WooCommerce). |
| **Version**     | 1.0.0                                                                                                                                                                      |
| **Category**    | Diagnostic Skills                                                                                                                                                          |
| **Trigger**     | On-demand / Auto (when platform vs backend gap > 20%)                                                                                                                      |

***

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly Discrepancy Analysis is superior:**

| Capability                       | Platform Dashboards                  | Attribuly                              | Why It Matters                                             |
| -------------------------------- | ------------------------------------ | -------------------------------------- | ---------------------------------------------------------- |
| **Source of Truth**              | Siloed, self-reporting               | ✅ Multi-touch, pixel + server-side     | Exposes over-reporting by individual ad networks           |
| **Cross-Platform Deduplication** | Overlaps conversions across channels | ✅ Deduplicates conversions globally    | Shows the true ROAS and CPA of your marketing mix          |
| **Backend Sync**                 | Modeled or estimated data            | ✅ Hard-synced with Shopify/WooCommerce | Validates that reported revenue equals actual cash in bank |

**Key Insight:** Ad platforms inherently claim as much credit as possible. Discrepancy analysis uncovers which channels are cannibalizing organic traffic or double-counting the same customer journey.

***

## When to Trigger This Skill

### Automatic Triggers

- When the gap between Platform Reported Conversions and Backend Orders exceeds 20%.
- When Attribuly's `new_order_roas` (ncROAS) drops while Platform ROAS remains artificially high.
- Triggered as a secondary diagnostic when `daily_marketing_pulse` flags revenue mismatches.

### Manual Triggers (User Commands)

- "Why don't my Meta ads conversions match Shopify?"
- "Analyze our attribution gap."
- "Are Google Ads over-reporting?"
- "Find the discrepancy between our ad spend and actual revenue."

### Context Triggers

- When the user expresses distrust in platform-reported numbers.
- During end-of-month or end-of-week reconciliation reporting.

***

## Skill Purpose

Deliver a clear diagnostic report that bridges the gap between ad platforms and the backend store:

1. **Gap Quantification** — Calculate the exact percentage difference between Platform, Attribuly, and Store metrics.
2. **Deduplication Analysis** — Highlight how much overlap exists between channels (e.g., Meta claiming credit for a Google search click).
3. **Tracking Health** — Identify broken pixels, missing UTMs, or server-side API failures causing data loss.
4. **Action Plan** — Recommend tracking fixes and budget reallocations based on the *true* data.

***

## Data Sources

### Primary APIs

#### 1. Attribution List (Detailed)
**Endpoint:** `POST /{version}/api/all-attribution/get-list`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Fetch Attribuly's deduplicated linear or first/last click attribution metrics, as well as platform-specific metrics including `ad_conversion_value` and `ad_roas`.

#### 2. Get Total Numbers (Aggregation)
**Endpoint:** `POST /{version}/api/all-attribution/get-list-sum`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Retrieve comprehensive total numbers for attribution reporting, enabling high-level comparisons between aggregated ad platform metrics and overall attributed performance.

#### 3. List Destinations (Server-Side Tracking Validation)
**Endpoint:** `POST /{version}/api/get/connection/destination`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Verify whether users have enabled server-side tracking functionality for signal transmission. If tracking is not fully configured, prompt the user to activate server-side tracking to decrease discrepancy.

**Required Parameters (Attribution List & Aggregation)**

| Parameter    | Type           | Required | Description                              |
| ------------ | -------------- | -------- | ---------------------------------------- |
| `start_date` | string         | Yes      | Start date (`YYYY-MM-DD`)                |
| `end_date`   | string         | Yes      | End date (`YYYY-MM-DD`)                  |
| `dimensions` | array\[string] | Yes      | Breakdowns (e.g., `channel`, `platform`) |
| `model`      | string         | Yes      | e.g., `linear`, `last_click`             |

**Primary Fields Used for Comparison**

| Field                          | Description                                    |
| ------------------------------ | ---------------------------------------------- |
| `channel` / `platform`         | Acquisition source                             |
| `conversions` (Attribuly) | Deduplicated purchases credited to the channel |
| `conversion_value` (Attribuly) | Deduplicated revenue credited |
| `ad_conversions` | Conversions claimed natively by ad platforms |
| `ad_conversion_value` | Revenue claimed natively by ad platforms |
| `ad_roas` | Return on ad spend calculated by ad platforms |
| `total_backend_orders` | Total actual store orders |

***

## Default Parameters

| Parameter    | Default Value | Notes                          |
| ------------ | ------------- | ------------------------------ |
| `version`    | `v2-4-2`      | API version                    |
| `start_date` | Today - 7 days   | Standard comparison window     |
| `end_date`   | Today - 1 day | Yesterday, explicitly excluding today |
| `model`      | `linear`      | Linear attribution             |
| `dimensions` | `["channel"]` | High-level discrepancy view    |

***

## Execution Steps

### Step 1: Validate Input

- Ensure `start_date <= end_date`.
- Set the attribution model to `linear` for baseline comparison.

### Step 2: Fetch Attribuly Deduplicated Data & Platform Metrics
- Call `/api/all-attribution/get-list` to get Attribuly's calculated conversions and revenue (`conversions`, `conversion_value`) alongside platform-reported metrics (`ad_conversion_value`, `ad_roas`) by channel.
- Call `/api/all-attribution/get-list-sum` to get total aggregated numbers for the entire account to compare overall efficiency.

### Step 3: Verify Server-Side Tracking Status
- Call `/api/get/connection/destination` to verify server-side tracking (CAPI, Conversions API) is active.
- Ensure `status: 1` and `server_config` has relevant events enabled (e.g., `checkout_completed: true`).
- Fetch total backend orders (store truth) to evaluate overall capture rate.

### Step 4: Calculate Discrepancies
- **Platform Over-reporting %** = `(ad_conversions - Conversions) / Conversions` (or using revenue/value equivalents)
- **Total Store Capture %** = `(Total Attributed Conversions / Total Backend Orders)`

### Step 5: Isolate Root Causes
- If a specific channel has > 30% over-reporting, flag for cross-channel overlap (e.g., Meta view-through vs Google click).
- If Total Store Capture is < 80% and server-side tracking is inactive or misconfigured, flag tracking loss and recommend enabling CAPI.

### Step 6: Generate Recommendations

- Recommend shifting budget away from high-discrepancy (over-reporting) channels towards channels with high true (Attribuly) ROAS.

***

## Key Metrics to Analyze

| Metric                         | Source      | Why It Matters                     |
| ------------------------------ | ----------- | ---------------------------------- |
| **Attribuly Conversions**      | Attribuly   | The deduplicated source of truth   |
| **Platform Conversions**       | Ad Network  | The native, often inflated, number |
| **Backend Orders**             | Shopify/Woo | The absolute financial truth       |
| **Discrepancy Variance %**     | Calculated  | Highlights who is stealing credit  |
| **True ROAS vs Platform ROAS** | Calculated  | Drives actual budget decisions     |

***

## Root Cause Analysis Logic

### Scenario 1: Platform > Attribuly (Over-reporting)

| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Meta claims 100 sales, Attribuly credits 60 | Meta view-through attribution taking credit for organic/Google search traffic. | For Enterprise users: Implement the **Full Impact Attribution Model** to enable view-through attribution capabilities for Meta campaigns, capturing conversions that occur after views without clicks. For others: Rely on Attribuly ROAS for scaling decisions and shorten Meta attribution window. |

### Scenario 2: Platform < Attribuly (Under-reporting)

| Condition                                       | Likely Cause                                                                          | Action                  |
| ----------------------------------------------- | ------------------------------------------------------------------------------------- | ----------------------- |
| Platform reports 20 sales, Attribuly credits 40 | Native pixel is broken or blocked by ad-blockers, but server-side tracking caught it. | Fix native pixel setup. |

***

## Expected Output

```markdown
# 🔍 Attribution Discrepancy Report
Date Range: [start] to [end]

## 1) Executive Summary
- **Total Backend Orders:** [X]
- **Total Attributed Orders:** [Y] (Capture Rate: XX%)
- **Primary Insight:** [e.g., Meta is over-reporting conversions by 45% compared to deduplicated reality.]

## 2) Discrepancy Breakdown by Channel

| Channel | Platform Reported | Attribuly (True) | Discrepancy Gap | Platform ROAS | True ROAS |
|---------|------------------:|-----------------:|----------------:|--------------:|----------:|
| Meta Ads | 120 | 85 | +41.1% 🔴 | 3.5x | 2.1x |
| Google Ads | 90 | 88 | +2.2% ✅ | 4.0x | 3.9x |

## 3) Tracking Health
- **Unattributed Orders:** [Z] orders could not be matched to a source.
- **Status:** [✅ Healthy / 🟡 Warning / 🔴 Critical tracking loss]

## 4) Recommended Actions
1. [Budget Action - e.g., Scale back Meta due to inflated ROAS]
2. [Tracking Action - e.g., Audit UTMs on recent TikTok campaigns]
```

***

## Error Handling Specifications

- **Missing Platform Data**: If ad account integration is broken, return: "Cannot compare discrepancy: \[Platform] account disconnected. Please re-authenticate in settings."
- **Rate Limit (`429`)**: Retry with exponential backoff (max 3 retries).
- **Empty Backend Data**: Ensure the date range isn't set to a future date or a period before the store was connected.

***

## Thresholds for Alerts

| Metric                                    | Warning (🟡)     | Critical (🔴)    |
| ----------------------------------------- | ---------------- | ---------------- |
| Platform Over-reporting Gap               | > 20%            | > 40%            |
| Store Capture Rate (Attributed / Backend) | < 90%            | < 75%            |
| True ROAS vs Platform ROAS divergence     | > 1.0 difference | > 2.0 difference |

***

## Example API Calls

### 1. Fetch Deduplicated Channel Attribution

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-17",
    "dimensions": ["channel"],
    "model": "linear",
    "goal": "purchase"
  }'
```

***

## Dependencies

| Dependency         | Type         | Purpose                                        |
| ------------------ | ------------ | ---------------------------------------------- |
| Attribuly Data API | External API | Fetch attribution and platform comparison data |
| Settings API       | External API | Verify store connection status                 |

***

## Related Skills

| Skill                          | When to Trigger                                                                  |
| ------------------------------ | -------------------------------------------------------------------------------- |
| `budget_optimization`          | Trigger when a platform is heavily over-reporting to adjust actual spend pacing. |
| `weekly_marketing_performance` | Serves as the parent skill that normally surfaces high-level ROAS metrics.       |

