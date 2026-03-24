# AllyClaw Skill Registry

This document defines all available skills, their triggers, and when to use each one.

***

## Skill Categories

### 1. Performance Analysis Skills

| Skill ID                       | Name                         | Trigger                                        | Status     |
| ------------------------------ | ---------------------------- | ---------------------------------------------- | ---------- |
| `weekly-marketing-performance` | Weekly Marketing Performance | Scheduled (Monday) / On-demand                 | ✅ Ready    |
| `daily-marketing-pulse`        | Daily Marketing Pulse        | Scheduled (Daily) / On-demand                  | ✅ Ready    |
| `google-ads-performance`       | Google Ads Performance       | On-demand / Auto (when Google issues detected) | ✅ Ready    |
| `meta-ads-performance`         | Meta Ads Performance         | On-demand / Auto (when Meta issues detected)   | ✅ Ready    |
| `tiktok_ads_performance`       | TikTok Ads Performance       | On-demand                                      | 🔜 Planned |

### 2. Creative Analysis Skills

| Skill ID                    | Name                      | Trigger                                  | Status     |
| --------------------------- | ------------------------- | ---------------------------------------- | ---------- |
| `google-creative-analysis`  | Google Creative Analysis  | On-demand / Auto (when CTR issues)       | ✅ Ready    |
| `meta-creative-analysis`    | Meta Creative Analysis    | On-demand / Auto (when creative fatigue) | 🔜 Planned |
| `creative-fatigue-detector` | Creative Fatigue Detector | Auto (frequency > threshold)             | 🔜 Planned |

### 3. Optimization Skills

| Skill ID                    | Name                      | Trigger                                          | Status  |
| --------------------------- | ------------------------- | ------------------------------------------------ | ------- |
| `budget-optimization`       | Budget Optimization       | On-demand / Auto (when MER off-target)           | ✅ Ready |
| `audience-optimization`     | Audience Optimization     | On-demand / Auto (when cannibalization detected) | ✅ Ready |
| `bid-strategy-optimization` | Bid Strategy Optimization | On-demand / Auto (when CPA/ROAS targets missed)  | ✅ Ready |

### 4. Diagnostic Skills

| Skill ID                  | Name                             | Trigger                           | Status     |
| ------------------------- | -------------------------------- | --------------------------------- | ---------- |
| `funnel-analysis`         | Funnel Analysis                  | On-demand / Auto (when CVR drops) | ✅ Ready    |
| `landing-page-analysis`   | Landing Page Analysis            | On-demand                         | ✅ Ready    |
| `attribution-discrepancy` | Attribution Discrepancy Analysis | On-demand                         | ✅ Ready    |

#### `landing-page-analysis`

1. **Skill Metadata**
   - **ID**: `landing-page-analysis`
   - **Version**: `v1.0.0`
   - **Category**: Diagnostic Skills
   - **Trigger**: On-demand / Auto (when top-of-funnel drop-off is high)

2. **When to Trigger**
   - **Automatic**: Trigger when Homepage → Product View drop-off exceeds 20% in `funnel-analysis`.
   - **Manual**: User asks "Why is this landing page not converting?" or "Analyze landing page performance".
   - **Context**: Trigger after new LP launch, major creative refresh, or channel-level CVR decline.

3. **Skill Purpose**
   - Diagnose landing-page performance issues by isolating weak pages, low-quality traffic sources, and engagement breakdowns that reduce downstream conversions.

4. **Data Sources**
   - **Endpoint A**: `POST /{version}/api/get/web-analysis/list`
     - Use `dimensions`: `["landing_page","channel","utm_campaign","utm_source","utm_medium"]`
     - Key fields: `landing_page`, `homepage_view_users`, `product_view_users`, `atc_users`, `purchases`, `purchases_rate`, `engagement_rate`, `event_per_session`, `spend`, `revenue`
   - **Endpoint B**: `POST /{version}/api/all-attribution/get-list`
     - Use for conversion value and channel-level attribution context by landing page dimension.

5. **Default Parameters**
   - `version`: `v2-4-2`
   - `start_date`: Today - 14 days
   - `end_date`: Today - 1 day (Yesterday, explicitly excluding today)
   - `dimensions`: `["landing_page","channel","utm_campaign"]`
   - `page_size`: `100`
   - `model`: `linear`
   - `goal`: `purchase`

6. **Execution Steps**
   - **Step 1**: Validate date range (`start_date <= end_date`, window <= 90 days).
   - **Step 2**: Fetch landing-page funnel dataset and confirm `code === 1`.
   - **Step 3**: Compute LP stage conversion rates:
     - LP View → Product View = `product_view_users / homepage_view_users`
     - Product View → Add-to-Cart = `atc_users / product_view_users`
     - Add-to-Cart → Purchase = `purchases / atc_users`
   - **Step 4**: Rank worst LPs by drop-off severity and revenue impact.
   - **Step 5**: Segment by `channel`, `utm_campaign`, `utm_source`, `utm_medium` to separate traffic-quality vs page-quality issues.
   - **Step 6**: Generate remediation recommendations with severity labels.

7. **Key Metrics**
   - `landing_page`
   - `homepage_view_users`
   - `product_view_users`
   - `atc_users`
   - `purchases`
   - `purchases_rate`
   - `engagement_rate`
   - `event_per_session`
   - `spend`
   - `revenue`

8. **Root Cause Analysis Logic**
   - **IF** LP View → Product View is weak and `engagement_rate` is low:
     - Probable mismatch between ad message and LP content, weak first fold, or poor page speed.
   - **IF** Product View is healthy but Add-to-Cart is weak:
     - Probable offer/pricing/PDP quality issue.
   - **IF** Add-to-Cart is healthy but Purchase is weak:
     - Probable checkout friction, shipping shock, or payment failure.
   - **IF** one channel drives most bad LP traffic:
     - Probable targeting/query intent mismatch; recommend channel-level refinement.

9. **Output Format**
   - **Section A**: Landing page health summary (top issues, severity, impacted revenue).
   - **Section B**: LP ranking table (views, progression rates, drop-off, spend, revenue).
   - **Section C**: Traffic source diagnosis by channel/campaign/source/medium.
   - **Section D**: Action plan (quick wins, structural fixes, validation tests).

10. **Thresholds**
    - **Warning**:
      - LP View → Product View drop-off > 20%
      - `engagement_rate` < 40%
    - **Critical**:
      - LP View → Product View drop-off > 35%
      - `engagement_rate` < 20%
      - High-spend LP with `purchases = 0`

11. **Example API Calls**
    ```bash
    curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/web-analysis/list" \
      -H "ApiKey: $ATTRIBULY_API_KEY" \
      -H "Content-Type: application/json" \
      -d '{
        "start_date": "2026-03-01",
        "end_date": "2026-03-17",
        "dimensions": ["landing_page", "channel", "utm_campaign", "utm_source", "utm_medium"]
      }'
    ```

12. **Related Skills**
    - `funnel-analysis` (parent diagnostic trigger)
    - `budget-optimization` (when spend is concentrated on low-performing LPs)
    - `attribution-discrepancy` (when LP-level purchases diverge from backend orders)

### 5. Product & Customer Skills

| Skill ID                    | Name                         | Trigger   | Status     |
| --------------------------- | ---------------------------- | --------- | ---------- |
| `product-performance`       | Product Performance Analysis | On-demand | 🔜 Planned |
| `customer-journey-analysis` | Customer Journey Analysis    | On-demand | 🔜 Planned |
| `ltv-analysis`              | LTV Analysis                 | On-demand | 🔜 Planned |

***

## Skill Trigger Matrix

### User Intent → Skill Mapping

| User Says                     | Primary Skill                  | Secondary Skills           |
| ----------------------------- | ------------------------------ | -------------------------- |
| "Weekly report"               | `weekly-marketing-performance` | -                          |
| "How did we do last week?"    | `weekly-marketing-performance` | -                          |
| "Daily update"                | `daily-marketing-pulse`        | -                          |
| "How's Google doing?"         | `google-ads-performance`       | `google-creative-analysis` |
| "Meta performance"            | `meta-ads-performance`         | `meta-creative-analysis`   |
| "Why did ROAS drop?"          | `weekly-marketing-performance` | Channel-specific skill     |
| "Creative fatigue?"           | `creative-fatigue-detector`    | `meta-creative-analysis`   |
| "Optimize budget"             | `budget-optimization`          | -                          |
| "Which products are winning?" | `product-performance`          | -                          |
| "Customer journey"            | `customer-journey-analysis`    | -                          |
| "Funnel issues"               | `funnel-analysis`              | `landing-page-analysis`    |

### Automatic Triggers

| Condition              | Triggered Skill                                     | Priority |
| ---------------------- | --------------------------------------------------- | -------- |
| Monday 09:00 AM        | `weekly-marketing-performance`                      | High     |
| Daily 09:00 AM         | `daily-marketing-pulse`                             | Medium   |
| ROAS drops >20%        | `weekly-marketing-performance` + channel drill-down | Critical |
| CPA increases >20%     | Channel-specific performance skill                  | High     |
| CTR drops >15%         | `creative-fatigue-detector`                         | Medium   |
| CVR drops >15%         | `funnel-analysis`                                   | High     |
| Spend >30% over budget | `budget-optimization`                               | Critical |

***

## Skill Chaining Logic

When one skill detects an issue, it can trigger related skills:

```
weekly-marketing-performance
├── IF Google Ads issue detected → google-ads-performance
│   └── IF CTR issue → google-creative-analysis
├── IF Meta Ads issue detected → meta-ads-performance
│   └── IF frequency high → meta-creative-analysis
├── IF CVR issue detected → funnel-analysis
│   └── IF landing page issue → landing-page-analysis
└── IF budget inefficiency → budget-optimization
```

***

## Skill Naming Convention

All capabilities are referenced via this directory pattern inside the skill bundle:

```
/openclaw-config/skills/attribuly-dtc-analyst/references/{capability-slug}.md
```

Example:

- `references/weekly-marketing-performance.md`
- `references/google-ads-performance.md`
- `references/meta-creative-analysis.md`

***

## Capability File Structure Template

All capability reference files in `references/` start with valid YAML frontmatter containing `name`, `version`, and `description`.

```markdown
---
name: example-capability-slug
version: 1.0.0
description: A short summary of what the capability does.
---
# Capability: [Capability Name]
```

Each capability reference file MUST include:

1. **Skill Metadata** - ID, version, category, trigger
2. **When to Trigger** - Automatic, manual, and context triggers
3. **Skill Purpose** - What the skill does
4. **Data Sources** - API endpoints and parameters
5. **Default Parameters** - Default values for API calls
6. **Execution Steps** - Step-by-step logic
7. **Key Metrics** - What to analyze
8. **Root Cause Analysis Logic** - Diagnostic decision trees
9. **Output Format** - Standardized report template
10. **Thresholds** - Warning and critical levels
11. **Example API Calls** - Ready-to-use curl commands
12. **Related Skills** - What skills to chain to

***

## Default API Parameters (Global)

These defaults apply to ALL skills unless overridden:

| Parameter   | Default Value | Notes                                                          |
| ----------- | ------------- | -------------------------------------------------------------- |
| `model`     | `linear`      | Linear attribution                                             |
| `goal`      | `purchase`    | Purchase conversions (use dynamic goal code from Settings API) |
| `version`   | `v2-4-2`      | API version                                                    |
| `page_size` | `100`         | Max records per page                                           |

**Base URL:** `https://data.api.attribuly.com`
**Authentication:** `ApiKey` header (Read from `ATTRIBULY_API_KEY` Environment Variable / Secret Manager. NEVER ask the user for this in chat.)

***

## Global API Endpoints

### 1. Conversion Goals API (Settings)

**Purpose:** Fetch available conversion goals dynamically. Use this to get the correct `goal` parameter for attribution queries instead of hardcoding.

**Endpoint:** `POST /{version}/api/get/setting-goals`

**Request:**

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/setting-goals" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{}'
```

**Response Example:**

```json
{
  "code": 1,
  "message": "Service succeed",
  "data": {
    "goals": [
      {
        "name": "Purchase",
        "code": "x4w0tc0elo0co1n9eftdalo100h8dp6k",
        "event_type": "checkout_completed",
        "limit_type": "no",
        "limit_value": ["1"],
        "data_source": 1,
        "conversion_num": 0
      },
      {
        "name": "Add to cart",
        "code": "x4w0tc0szc0co1ngbm1iidg3001z2nn1",
        "event_type": "product_added_to_cart",
        "limit_type": "no",
        "limit_value": null,
        "data_source": 1,
        "conversion_num": 0
      }
    ]
  }
}
```

**Response Fields:**

| Field            | Type    | Description                                                                     |
| ---------------- | ------- | ------------------------------------------------------------------------------- |
| `name`           | string  | Human-readable goal name (e.g., "Purchase", "Add to cart")                      |
| `code`           | string  | Unique goal code to use in `goal` parameter for attribution APIs                |
| `event_type`     | string  | The underlying event type (e.g., `checkout_completed`, `product_added_to_cart`) |
| `limit_type`     | string  | Limit type for the goal                                                         |
| `limit_value`    | array   | Limit values                                                                    |
| `data_source`    | integer | Data source identifier                                                          |
| `conversion_num` | integer | Number of conversions tracked                                                   |

**Common Event Types:**

| Event Type              | Description              |
| ----------------------- | ------------------------ |
| `checkout_completed`    | Purchase/Order completed |
| `product_added_to_cart` | Add to cart event        |
| `checkout_started`      | Checkout initiated       |
| `page_viewed`           | Page view                |
| `lead`                  | Lead form submission     |

**Usage in Skills:**

1. **On initialization**, call the Conversion Goals API to fetch available goals
2. **Map goal names to codes** — Use the `code` field when calling attribution APIs
3. **Default to "Purchase"** — If no specific goal is requested, use the goal with `event_type: checkout_completed`

**Example: Getting the Purchase Goal Code**

```javascript
// Pseudo-code for skill initialization
const goals = await fetchGoals();
const purchaseGoal = goals.find(g => g.event_type === 'checkout_completed');
const goalCode = purchaseGoal?.code || 'purchase'; // fallback to 'purchase'
```

***

### 2. Connected Sources API (Account Discovery)

**Purpose:** Retrieve connected ad platform accounts (Google, Meta, TikTok, etc.) to obtain the required `account_id` for platform-specific queries.

**Endpoint:** `POST /{version}/api/get/connection/source`

**Request:**

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/connection/source" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "platform_type": "google"
  }'
```

**Request Parameters:**

| Parameter       | Type   | Required | Description                                                                                                                                               |
| --------------- | ------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `platform_type` | string | Optional | Filter by platform: `facebook`, `google`, `tiktok`, `bing`, `snapchat`, `klaviyo`, `impact`, `shareasale`, `cybertargeter`, `cartsee`, `omnisend`, `awin` |

**Response Example:**

```json
{
  "code": 1,
  "message": "Service succeed",
  "data": {
    "records": [
      {
        "id": 115,
        "account_id": "pk_1234567890",
        "name": "Attribuly",
        "platform_type": "klaviyo",
        "currency": "USD",
        "connected": 1,
        "trace_status": 0
      }
    ]
  }
}
```

**Response Fields:**

| Field           | Type    | Description                                                  |
| --------------- | ------- | ------------------------------------------------------------ |
| `id`            | integer | Internal record ID                                           |
| `account_id`    | string  | Platform account ID (use this for platform-specific queries) |
| `name`          | string  | Account display name                                         |
| `platform_type` | string  | Platform identifier                                          |
| `currency`      | string  | Account currency                                             |
| `connected`     | integer | Connection status (1 = connected)                            |
| `trace_status`  | integer | Tracking status                                              |

**Usage in Skills:**

1. **On initialization**, call the Connected Sources API to discover available ad accounts
2. **Extract** **`account_id`** for the target platform (e.g., Google, Meta)
3. **Use** **`account_id`** in platform-specific query APIs

***

### 3. Attribution Report APIs

#### Get Total Numbers (Summary)

**Endpoint:** `POST /{version}/api/all-attribution/get-list-sum`

#### Get Attribution Report (Detailed)

**Endpoint:** `POST /{version}/api/all-attribution/get-list`

#### Get Ad Analysis (Campaign/Ad Set/Ad Level)

**Endpoint:** `POST /{version}/api/get/ad-analysis/list`

***

### 4. Platform-Specific Query APIs

#### Google Ads Query API

**Purpose:** Execute GAQL (Google Ads Query Language) queries to retrieve detailed Google Ads data including search terms, quality scores, impression share, and more.

**Endpoint:** `POST /{version}/api/source/google-query`

**Request:**

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/google-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "6622546829",
    "gaql": "SELECT search_term_view.search_term, campaign.name, metrics.impressions, metrics.clicks, metrics.cost_micros FROM search_term_view WHERE segments.date BETWEEN '\''2025-03-01'\'' AND '\''2025-03-17'\'' ORDER BY metrics.cost_micros DESC LIMIT 100"
  }'
```

**Request Parameters:**

| Parameter    | Type   | Required | Description                                                |
| ------------ | ------ | -------- | ---------------------------------------------------------- |
| `account_id` | string | Yes      | Google Ads customer ID (obtain from Connected Sources API) |
| `gaql`       | string | Yes      | Google Ads Query Language query string                     |

**Response Example:**

```json
{
  "code": 1,
  "message": "Service succeed",
  "data": {
    "record": [
      {
        "account_id": "6622546829",
        "error": null,
        "results": [
          {
            "campaign": {
              "id": "1533978646",
              "name": "Search - Brand"
            },
            "adGroup": {
              "id": "56692376537",
              "name": "Attribuly - Top Brand Terms"
            },
            "metrics": {
              "impressions": "78",
              "clicks": "27",
              "costMicros": "318470820",
              "conversions": 0,
              "ctr": 0.346
            },
            "searchTermView": {
              "searchTerm": "attribuly",
              "status": "ADDED"
            }
          }
        ],
        "success": true
      }
    ]
  }
}
```

**Important Notes:**

- `costMicros` is in micros (divide by 1,000,000 to get actual cost)
- Google does NOT disclose \~50% of search terms (shown as "(other)")
- For PMax campaigns, use `campaign_search_term_insight` resource

#### Meta Ads Query API

**Purpose:** Query Meta (Facebook/Instagram) Ads data including frequency, reach, video metrics, and placement breakdowns.

**Endpoint:** `POST /{version}/api/source/meta-query`

**Request:**

```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "act_123456789",
    "level": "ad",
    "fields": ["campaign_name", "adset_name", "ad_name", "impressions", "reach", "frequency", "spend"],
    "time_range": {
      "since": "2025-03-01",
      "until": "2025-03-17"
    }
  }'
```

***

## Error Handling & Rate Limiting

### Error Response Format

All APIs return errors in a consistent format:

```json
{
  "code": 0,
  "message": "Error description",
  "data": null
}
```

### Common Error Codes

| Code  | Meaning       | Action                        |
| ----- | ------------- | ----------------------------- |
| `0`   | General error | Check `message` for details   |
| `1`   | Success       | Process response data         |
| `401` | Unauthorized  | Verify ApiKey                 |
| `429` | Rate limited  | Implement exponential backoff |
| `500` | Server error  | Retry with backoff            |

### Rate Limits

| API Type         | Limit          | Window                      |
| ---------------- | -------------- | --------------------------- |
| Attribuly APIs   | 100 requests   | Per minute                  |
| Google Query API | 1,000 requests | Per 100 seconds per account |
| Meta Query API   | 200 calls      | Per hour per ad account     |

### Recommended Retry Strategy

```javascript
async function apiCallWithRetry(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (error.code === 429 && i < maxRetries - 1) {
        const delay = Math.pow(2, i) * 1000; // Exponential backoff
        console.log(`[RATE_LIMIT] Retrying in ${delay}ms...`);
        await sleep(delay);
        continue;
      }
      throw error;
    }
  }
}
```

***

## Data Validation

### Required Validations

1. **Date Range**: Ensure `start_date` <= `end_date` and range <= 90 days
2. **Account ID**: Verify account exists via Connected Sources API before querying
3. **Response Code**: Always check `code === 1` before processing data
4. **Empty Results**: Handle empty `results` arrays gracefully

### Validation Example

```javascript
function validateApiResponse(response) {
  if (response.code !== 1) {
    console.error(`[API_ERROR] ${response.message}`);
    return { success: false, error: response.message };
  }
  if (!response.data || !response.data.records?.length) {
    console.warn('[API_WARNING] No data returned');
    return { success: true, data: [] };
  }
  return { success: true, data: response.data.records };
}
```

***

## Logging Best Practices

### Log Levels

| Level   | When to Use                                             |
| ------- | ------------------------------------------------------- |
| `DEBUG` | API request/response details, intermediate calculations |
| `INFO`  | Skill execution start/end, key milestones               |
| `WARN`  | Empty results, approaching rate limits, data anomalies  |
| `ERROR` | API failures, validation errors, unexpected exceptions  |

### Structured Logging Format

```javascript
console.log(JSON.stringify({
  timestamp: new Date().toISOString(),
  level: 'INFO',
  skill: 'google-ads-performance',
  action: 'fetch_search_terms',
  account_id: '6622546829',
  date_range: { start: '2025-03-01', end: '2025-03-17' },
  result_count: 150,
  duration_ms: 1234
}));
```

*See individual skill files for detailed usage.*
