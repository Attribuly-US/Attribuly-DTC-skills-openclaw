# AllyClaw Skill Registry

This document defines all available skills, their triggers, and when to use each one.

---

## Skill Categories

### 1. Performance Analysis Skills
| Skill ID | Name | Trigger | Status |
|----------|------|---------|--------|
| `weekly_marketing_performance` | Weekly Marketing Performance | Scheduled (Monday) / On-demand | ✅ Ready |
| `daily_marketing_pulse` | Daily Marketing Pulse | Scheduled (Daily) / On-demand | ✅ Ready |
| `google_ads_performance` | Google Ads Performance | On-demand / Auto (when Google issues detected) | ✅ Ready |
| `meta_ads_performance` | Meta Ads Performance | On-demand / Auto (when Meta issues detected) | ✅ Ready |
| `tiktok_ads_performance` | TikTok Ads Performance | On-demand | 🔜 Planned |

### 2. Creative Analysis Skills
| Skill ID | Name | Trigger | Status |
|----------|------|---------|--------|
| `google_creative_analysis` | Google Creative Analysis | On-demand / Auto (when CTR issues) | 🔜 Planned |
| `meta_creative_analysis` | Meta Creative Analysis | On-demand / Auto (when creative fatigue) | 🔜 Planned |
| `creative_fatigue_detector` | Creative Fatigue Detector | Auto (frequency > threshold) | 🔜 Planned |

### 3. Optimization Skills
| Skill ID | Name | Trigger | Status |
|----------|------|---------|--------|
| `budget_optimization` | Budget Optimization | On-demand / Auto (when MER off-target) | ✅ Ready |
| `audience_optimization` | Audience Optimization | On-demand / Auto (when cannibalization detected) | ✅ Ready |
| `bid_strategy_optimization` | Bid Strategy Optimization | On-demand / Auto (when CPA/ROAS targets missed) | ✅ Ready |

### 4. Diagnostic Skills
| Skill ID | Name | Trigger | Status |
|----------|------|---------|--------|
| `funnel_analysis` | Funnel Analysis | On-demand / Auto (when CVR drops) | 🔜 Planned |
| `landing_page_analysis` | Landing Page Analysis | On-demand | 🔜 Planned |
| `attribution_discrepancy` | Attribution Discrepancy Analysis | On-demand | 🔜 Planned |

### 5. Product & Customer Skills
| Skill ID | Name | Trigger | Status |
|----------|------|---------|--------|
| `product_performance` | Product Performance Analysis | On-demand | 🔜 Planned |
| `customer_journey_analysis` | Customer Journey Analysis | On-demand | 🔜 Planned |
| `ltv_analysis` | LTV Analysis | On-demand | 🔜 Planned |

---

## Skill Trigger Matrix

### User Intent → Skill Mapping

| User Says | Primary Skill | Secondary Skills |
|-----------|---------------|------------------|
| "Weekly report" | `weekly_marketing_performance` | - |
| "How did we do last week?" | `weekly_marketing_performance` | - |
| "Daily update" | `daily_marketing_pulse` | - |
| "How's Google doing?" | `google_ads_performance` | `google_creative_analysis` |
| "Meta performance" | `meta_ads_performance` | `meta_creative_analysis` |
| "Why did ROAS drop?" | `weekly_marketing_performance` | Channel-specific skill |
| "Creative fatigue?" | `creative_fatigue_detector` | `meta_creative_analysis` |
| "Optimize budget" | `budget_optimization` | - |
| "Which products are winning?" | `product_performance` | - |
| "Customer journey" | `customer_journey_analysis` | - |
| "Funnel issues" | `funnel_analysis` | `landing_page_analysis` |

### Automatic Triggers

| Condition | Triggered Skill | Priority |
|-----------|-----------------|----------|
| Monday 09:00 AM | `weekly_marketing_performance` | High |
| Daily 09:00 AM | `daily_marketing_pulse` | Medium |
| ROAS drops >20% | `weekly_marketing_performance` + channel drill-down | Critical |
| CPA increases >20% | Channel-specific performance skill | High |
| CTR drops >15% | `creative_fatigue_detector` | Medium |
| CVR drops >15% | `funnel_analysis` | High |
| Spend >30% over budget | `budget_optimization` | Critical |

---

## Skill Chaining Logic

When one skill detects an issue, it can trigger related skills:

```
weekly_marketing_performance
├── IF Google Ads issue detected → google_ads_performance
│   └── IF CTR issue → google_creative_analysis
├── IF Meta Ads issue detected → meta_ads_performance
│   └── IF frequency high → meta_creative_analysis
├── IF CVR issue detected → funnel_analysis
│   └── IF landing page issue → landing_page_analysis
└── IF budget inefficiency → budget_optimization
```

---

## Skill File Naming Convention

All skill files should follow this pattern:
```
/openclaw-config/skills/{skill_id}.md
```

Example:
- `weekly_marketing_performance.md`
- `google_ads_performance.md`
- `meta_creative_analysis.md`

---

## Skill File Structure Template

Each skill file MUST include:

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

---

## Default API Parameters (Global)

These defaults apply to ALL skills unless overridden:

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `model` | `linear` | Full Impact attribution |
| `goal` | `purchase` | Purchase conversions (use dynamic goal code from Settings API) |
| `version` | `v2-4-2` | API version |
| `page_size` | `100` | Max records per page |

**Base URL:** `https://data.api.attribuly.com`
**Authentication:** `ApiKey` header

---

## Global API Endpoints

### 1. Conversion Goals API (Settings)

**Purpose:** Fetch available conversion goals dynamically. Use this to get the correct `goal` parameter for attribution queries instead of hardcoding.

**Endpoint:** `POST /{version}/api/get/setting-goals`

**Request:**
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/setting-goals" \
  -H "ApiKey: YOUR_API_KEY" \
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

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Human-readable goal name (e.g., "Purchase", "Add to cart") |
| `code` | string | Unique goal code to use in `goal` parameter for attribution APIs |
| `event_type` | string | The underlying event type (e.g., `checkout_completed`, `product_added_to_cart`) |
| `limit_type` | string | Limit type for the goal |
| `limit_value` | array | Limit values |
| `data_source` | integer | Data source identifier |
| `conversion_num` | integer | Number of conversions tracked |

**Common Event Types:**

| Event Type | Description |
|------------|-------------|
| `checkout_completed` | Purchase/Order completed |
| `product_added_to_cart` | Add to cart event |
| `checkout_started` | Checkout initiated |
| `page_viewed` | Page view |
| `lead` | Lead form submission |

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

---

### 2. Attribution Report APIs

#### Get Total Numbers (Summary)
**Endpoint:** `POST /{version}/api/all-attribution/get-list-sum`

#### Get Attribution Report (Detailed)
**Endpoint:** `POST /{version}/api/all-attribution/get-list`

#### Get Ad Analysis (Campaign/Ad Set/Ad Level)
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`

*See individual skill files for detailed usage.*
