# Missing Data API Reference

This document provides detailed API interfaces for the missing data fields that would enhance AllyClaw's analysis capabilities.

---

## Table of Contents
1. [Google Ads API - Missing Data](#google-ads-api---missing-data)
   - [Search Terms Report](#1-search-terms-report)
   - [Quality Score & Keyword Data](#2-quality-score--keyword-data)
   - [Impression Share Metrics](#3-impression-share-metrics)
   - [Device & Geographic Breakdown](#4-device--geographic-breakdown)
   - [PMax Asset Performance](#5-pmax-asset-performance)
2. [Meta Ads API - Missing Data](#meta-ads-api---missing-data)
   - [Frequency & Reach](#1-frequency--reach)
   - [Video Metrics](#2-video-metrics-thruplay-hook-rate-hold-rate)
   - [Placement Breakdown](#3-placement-breakdown)
   - [Demographic Breakdown](#4-demographic-breakdown-age-gender)
   - [Device Breakdown](#5-device-breakdown)

---

# Google Ads API - Missing Data

**API Version:** v17 (latest)  
**Base URL:** `https://googleads.googleapis.com/v17/customers/{customer_id}/googleAds:searchStream`  
**Authentication:** OAuth 2.0 Bearer Token  
**Query Language:** GAQL (Google Ads Query Language)

---

## 1. Search Terms Report

### Purpose
Identify actual search queries that triggered your ads. Critical for finding wasted spend on irrelevant queries.

### API Resource
`search_term_view`

### GAQL Query
```sql
SELECT
  search_term_view.search_term,
  search_term_view.status,
  search_term_view.ad_group,
  campaign.name,
  campaign.id,
  ad_group.name,
  ad_group.id,
  metrics.impressions,
  metrics.clicks,
  metrics.cost_micros,
  metrics.conversions,
  metrics.conversions_value,
  metrics.ctr,
  metrics.average_cpc
FROM search_term_view
WHERE segments.date BETWEEN '2025-03-01' AND '2025-03-17'
  AND metrics.impressions > 0
ORDER BY metrics.cost_micros DESC
LIMIT 1000
```

### Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `search_term_view.search_term` | STRING | The actual search query |
| `search_term_view.status` | ENUM | ADDED, EXCLUDED, ADDED_EXCLUDED, NONE |
| `metrics.impressions` | INT64 | Number of impressions |
| `metrics.clicks` | INT64 | Number of clicks |
| `metrics.cost_micros` | INT64 | Cost in micros (divide by 1,000,000) |
| `metrics.conversions` | DOUBLE | Number of conversions |
| `metrics.conversions_value` | DOUBLE | Conversion value |

### Example cURL Request
```bash
curl -X POST \
  'https://googleads.googleapis.com/v17/customers/1234567890/googleAds:searchStream' \
  -H 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
  -H 'developer-token: YOUR_DEVELOPER_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "query": "SELECT search_term_view.search_term, campaign.name, ad_group.name, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions FROM search_term_view WHERE segments.date BETWEEN '\''2025-03-01'\'' AND '\''2025-03-17'\'' ORDER BY metrics.cost_micros DESC LIMIT 1000"
  }'
```

### Important Notes
- Google does NOT disclose search terms for ~50% of queries (privacy reasons)
- These are shown as "(other)" in reports
- For PMax campaigns, use `campaign_search_term_insight` resource instead

---

## 2. Quality Score & Keyword Data

### Purpose
Diagnose why CPC is high or why ads aren't showing. Quality Score is 1-10 scale.

### API Resource
`keyword_view` (with `ad_group_criterion` attributes)

### GAQL Query
```sql
SELECT
  ad_group_criterion.keyword.text,
  ad_group_criterion.keyword.match_type,
  ad_group_criterion.quality_info.quality_score,
  ad_group_criterion.quality_info.creative_quality_score,
  ad_group_criterion.quality_info.post_click_quality_score,
  ad_group_criterion.quality_info.search_predicted_ctr,
  campaign.name,
  campaign.id,
  ad_group.name,
  ad_group.id,
  metrics.impressions,
  metrics.clicks,
  metrics.cost_micros,
  metrics.conversions,
  metrics.average_cpc,
  metrics.ctr,
  metrics.historical_quality_score,
  metrics.historical_creative_quality_score,
  metrics.historical_landing_page_quality_score,
  metrics.historical_search_predicted_ctr
FROM keyword_view
WHERE segments.date BETWEEN '2025-03-01' AND '2025-03-17'
  AND ad_group_criterion.status = 'ENABLED'
  AND metrics.impressions > 0
ORDER BY metrics.cost_micros DESC
LIMIT 1000
```

### Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `ad_group_criterion.keyword.text` | STRING | The keyword text |
| `ad_group_criterion.keyword.match_type` | ENUM | EXACT, PHRASE, BROAD |
| `ad_group_criterion.quality_info.quality_score` | INT32 | Current Quality Score (1-10) |
| `ad_group_criterion.quality_info.creative_quality_score` | ENUM | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE |
| `ad_group_criterion.quality_info.post_click_quality_score` | ENUM | Landing page experience |
| `ad_group_criterion.quality_info.search_predicted_ctr` | ENUM | Expected CTR |
| `metrics.historical_quality_score` | DOUBLE | Historical QS for date range |

### Quality Score Components

| Component | Field | Values |
|-----------|-------|--------|
| **Expected CTR** | `search_predicted_ctr` | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE |
| **Ad Relevance** | `creative_quality_score` | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE |
| **Landing Page Exp.** | `post_click_quality_score` | BELOW_AVERAGE, AVERAGE, ABOVE_AVERAGE |

### Example cURL Request
```bash
curl -X POST \
  'https://googleads.googleapis.com/v17/customers/1234567890/googleAds:searchStream' \
  -H 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
  -H 'developer-token: YOUR_DEVELOPER_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "query": "SELECT ad_group_criterion.keyword.text, ad_group_criterion.quality_info.quality_score, ad_group_criterion.quality_info.creative_quality_score, ad_group_criterion.quality_info.post_click_quality_score, ad_group_criterion.quality_info.search_predicted_ctr, campaign.name, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions FROM keyword_view WHERE segments.date BETWEEN '\''2025-03-01'\'' AND '\''2025-03-17'\'' AND metrics.impressions > 0 ORDER BY metrics.cost_micros DESC LIMIT 1000"
  }'
```

---

## 3. Impression Share Metrics

### Purpose
Identify lost opportunity due to budget or rank. Shows how often your ads are shown vs. eligible.

### API Resource
`campaign` or `ad_group`

### GAQL Query (Campaign Level)
```sql
SELECT
  campaign.name,
  campaign.id,
  campaign.advertising_channel_type,
  metrics.search_impression_share,
  metrics.search_budget_lost_impression_share,
  metrics.search_rank_lost_impression_share,
  metrics.search_absolute_top_impression_share,
  metrics.search_top_impression_share,
  metrics.search_budget_lost_absolute_top_impression_share,
  metrics.search_budget_lost_top_impression_share,
  metrics.search_rank_lost_absolute_top_impression_share,
  metrics.search_rank_lost_top_impression_share,
  metrics.impressions,
  metrics.clicks,
  metrics.cost_micros,
  metrics.conversions
FROM campaign
WHERE segments.date BETWEEN '2025-03-01' AND '2025-03-17'
  AND campaign.advertising_channel_type = 'SEARCH'
  AND metrics.impressions > 0
ORDER BY metrics.cost_micros DESC
```

### Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `metrics.search_impression_share` | DOUBLE | % of impressions received vs. eligible (0-1) |
| `metrics.search_budget_lost_impression_share` | DOUBLE | % lost due to budget |
| `metrics.search_rank_lost_impression_share` | DOUBLE | % lost due to Ad Rank |
| `metrics.search_absolute_top_impression_share` | DOUBLE | % shown in absolute top position |
| `metrics.search_top_impression_share` | DOUBLE | % shown in top positions |

### Interpretation Guide

| Metric | Good | Warning | Critical |
|--------|------|---------|----------|
| Search IS | >80% | 50-80% | <50% |
| Budget Lost IS | <10% | 10-30% | >30% |
| Rank Lost IS | <20% | 20-40% | >40% |

---

## 4. Device & Geographic Breakdown

### Purpose
Optimize bids and budgets by device type and location.

### GAQL Query (Device Breakdown)
```sql
SELECT
  campaign.name,
  campaign.id,
  segments.device,
  metrics.impressions,
  metrics.clicks,
  metrics.cost_micros,
  metrics.conversions,
  metrics.conversions_value,
  metrics.ctr,
  metrics.average_cpc
FROM campaign
WHERE segments.date BETWEEN '2025-03-01' AND '2025-03-17'
  AND metrics.impressions > 0
ORDER BY campaign.id, segments.device
```

### Device Values
- `MOBILE`
- `DESKTOP`
- `TABLET`
- `CONNECTED_TV`
- `OTHER`

### GAQL Query (Geographic Breakdown)
```sql
SELECT
  campaign.name,
  campaign.id,
  geographic_view.country_criterion_id,
  geographic_view.location_type,
  metrics.impressions,
  metrics.clicks,
  metrics.cost_micros,
  metrics.conversions,
  metrics.conversions_value
FROM geographic_view
WHERE segments.date BETWEEN '2025-03-01' AND '2025-03-17'
  AND metrics.impressions > 0
ORDER BY metrics.cost_micros DESC
LIMIT 100
```

---

## 5. PMax Asset Performance

### Purpose
Identify winning assets (headlines, descriptions, images, videos) in Performance Max campaigns.

### API Resource
`asset_group_asset` and `asset_group_listing_group_filter`

### GAQL Query
```sql
SELECT
  asset_group.name,
  asset_group.id,
  asset_group_asset.asset,
  asset_group_asset.field_type,
  asset_group_asset.performance_label,
  asset_group_asset.status,
  campaign.name,
  campaign.id
FROM asset_group_asset
WHERE campaign.advertising_channel_type = 'PERFORMANCE_MAX'
  AND asset_group_asset.status = 'ENABLED'
```

### Performance Labels
- `PENDING` - Not enough data
- `LOW` - Underperforming
- `GOOD` - Average performance
- `BEST` - Top performing

### Asset Field Types
- `HEADLINE`
- `LONG_HEADLINE`
- `DESCRIPTION`
- `MARKETING_IMAGE`
- `SQUARE_MARKETING_IMAGE`
- `PORTRAIT_MARKETING_IMAGE`
- `LOGO`
- `LANDSCAPE_LOGO`
- `YOUTUBE_VIDEO`
- `CALL_TO_ACTION_SELECTION`

---

# Meta Ads API - Missing Data

**API Version:** v21.0 (latest)  
**Base URL:** `https://graph.facebook.com/v21.0`  
**Authentication:** Access Token (ads_read permission)

---

## 1. Frequency & Reach

### Purpose
Detect ad fatigue and audience saturation. Critical for Meta optimization.

### API Endpoint
```
GET /{ad_account_id}/insights
GET /{campaign_id}/insights
GET /{adset_id}/insights
GET /{ad_id}/insights
```

### Request Parameters
```json
{
  "level": "ad",
  "fields": [
    "campaign_name",
    "campaign_id",
    "adset_name",
    "adset_id",
    "ad_name",
    "ad_id",
    "impressions",
    "reach",
    "frequency",
    "spend",
    "clicks",
    "cpm",
    "cpc",
    "ctr"
  ],
  "time_range": {
    "since": "2025-03-01",
    "until": "2025-03-17"
  }
}
```

### Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `reach` | INT | Unique users who saw the ad |
| `frequency` | FLOAT | Average times each person saw the ad (impressions/reach) |
| `impressions` | INT | Total impressions |

### Example cURL Request
```bash
curl -G \
  'https://graph.facebook.com/v21.0/act_123456789/insights' \
  -d 'level=ad' \
  -d 'fields=campaign_name,adset_name,ad_name,impressions,reach,frequency,spend,clicks,cpm,ctr' \
  -d 'time_range={"since":"2025-03-01","until":"2025-03-17"}' \
  -d 'access_token=YOUR_ACCESS_TOKEN'
```

### Frequency Thresholds for Fatigue Detection

| Frequency | Status | Action |
|-----------|--------|--------|
| 1-2 | ✅ Healthy | Continue |
| 2-3 | 🟡 Monitor | Watch for CTR decline |
| 3-5 | 🟠 Warning | Consider refreshing creative |
| >5 | 🔴 Fatigued | Pause or replace creative |

### Important Note (June 2025 Update)
Meta now limits `reach` data to **13 months (394 days)** when used with breakdown dimensions (age, gender, placement, etc.).

---

## 2. Video Metrics (ThruPlay, Hook Rate, Hold Rate)

### Purpose
Analyze video ad performance. Calculate Hook Rate and Hold Rate.

### API Endpoint
```
GET /{ad_id}/insights
```

### Request Parameters
```json
{
  "level": "ad",
  "fields": [
    "ad_name",
    "ad_id",
    "impressions",
    "reach",
    "video_play_actions",
    "video_p25_watched_actions",
    "video_p50_watched_actions",
    "video_p75_watched_actions",
    "video_p95_watched_actions",
    "video_p100_watched_actions",
    "video_thruplay_watched_actions",
    "video_avg_time_watched_actions",
    "video_continuous_2_sec_watched_actions",
    "video_30_sec_watched_actions"
  ],
  "time_range": {
    "since": "2025-03-01",
    "until": "2025-03-17"
  }
}
```

### Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `video_play_actions` | ARRAY | Number of times video started playing |
| `video_p25_watched_actions` | ARRAY | Watched 25% of video |
| `video_p50_watched_actions` | ARRAY | Watched 50% of video |
| `video_p75_watched_actions` | ARRAY | Watched 75% of video |
| `video_p95_watched_actions` | ARRAY | Watched 95% of video |
| `video_p100_watched_actions` | ARRAY | Watched 100% of video |
| `video_thruplay_watched_actions` | ARRAY | Watched to completion OR 15+ seconds |
| `video_avg_time_watched_actions` | ARRAY | Average watch time in seconds |
| `video_continuous_2_sec_watched_actions` | ARRAY | 2+ seconds continuous watch |
| `video_30_sec_watched_actions` | ARRAY | 30+ seconds watched |

### Calculated Metrics (Formulas)

#### Hook Rate (First 3 seconds)
```
Hook Rate = video_continuous_2_sec_watched_actions / impressions × 100
```
**Benchmark:** 20-25% is good, >30% is excellent

#### Hold Rate (Retention)
```
Hold Rate = video_thruplay_watched_actions / video_play_actions × 100
```
**Benchmark:** 40-50% is good, >50% is excellent

#### ThruPlay Rate
```
ThruPlay Rate = video_thruplay_watched_actions / impressions × 100
```

### Example cURL Request
```bash
curl -G \
  'https://graph.facebook.com/v21.0/act_123456789/insights' \
  -d 'level=ad' \
  -d 'fields=ad_name,impressions,video_play_actions,video_thruplay_watched_actions,video_p25_watched_actions,video_p50_watched_actions,video_p75_watched_actions,video_p100_watched_actions,video_avg_time_watched_actions,video_continuous_2_sec_watched_actions' \
  -d 'time_range={"since":"2025-03-01","until":"2025-03-17"}' \
  -d 'access_token=YOUR_ACCESS_TOKEN'
```

### Response Structure
```json
{
  "data": [
    {
      "ad_name": "Video Ad 1",
      "impressions": "10000",
      "video_play_actions": [
        {
          "action_type": "video_view",
          "value": "8000"
        }
      ],
      "video_thruplay_watched_actions": [
        {
          "action_type": "video_view",
          "value": "3500"
        }
      ],
      "video_p25_watched_actions": [
        {
          "action_type": "video_view",
          "value": "6000"
        }
      ]
    }
  ]
}
```

---

## 3. Placement Breakdown

### Purpose
Optimize budget allocation across placements (Feed, Stories, Reels, Audience Network).

### API Endpoint
```
GET /{ad_account_id}/insights
```

### Request Parameters
```json
{
  "level": "ad",
  "fields": [
    "campaign_name",
    "adset_name",
    "ad_name",
    "impressions",
    "reach",
    "clicks",
    "spend",
    "cpm",
    "ctr",
    "conversions",
    "cost_per_conversion"
  ],
  "breakdowns": ["publisher_platform", "platform_position"],
  "time_range": {
    "since": "2025-03-01",
    "until": "2025-03-17"
  }
}
```

### Breakdown Values

#### `publisher_platform`
- `facebook`
- `instagram`
- `messenger`
- `audience_network`

#### `platform_position`
- `feed`
- `story`
- `reels`
- `explore`
- `search`
- `marketplace`
- `video_feeds`
- `right_hand_column`
- `instant_article`
- `instream_video`
- `rewarded_video`
- `an_classic`

### Example cURL Request
```bash
curl -G \
  'https://graph.facebook.com/v21.0/act_123456789/insights' \
  -d 'level=ad' \
  -d 'fields=ad_name,impressions,clicks,spend,cpm,ctr,conversions' \
  -d 'breakdowns=["publisher_platform","platform_position"]' \
  -d 'time_range={"since":"2025-03-01","until":"2025-03-17"}' \
  -d 'access_token=YOUR_ACCESS_TOKEN'
```

### Response Structure
```json
{
  "data": [
    {
      "ad_name": "Ad 1",
      "publisher_platform": "facebook",
      "platform_position": "feed",
      "impressions": "5000",
      "clicks": "150",
      "spend": "50.00",
      "cpm": "10.00",
      "ctr": "3.00"
    },
    {
      "ad_name": "Ad 1",
      "publisher_platform": "instagram",
      "platform_position": "story",
      "impressions": "3000",
      "clicks": "90",
      "spend": "35.00",
      "cpm": "11.67",
      "ctr": "3.00"
    }
  ]
}
```

---

## 4. Demographic Breakdown (Age, Gender)

### Purpose
Analyze performance by age and gender to optimize targeting.

### Request Parameters
```json
{
  "level": "ad",
  "fields": [
    "campaign_name",
    "adset_name",
    "ad_name",
    "impressions",
    "reach",
    "clicks",
    "spend",
    "conversions",
    "cost_per_conversion"
  ],
  "breakdowns": ["age", "gender"],
  "time_range": {
    "since": "2025-03-01",
    "until": "2025-03-17"
  }
}
```

### Breakdown Values

#### `age`
- `13-17`
- `18-24`
- `25-34`
- `35-44`
- `45-54`
- `55-64`
- `65+`

#### `gender`
- `male`
- `female`
- `unknown`

### Example cURL Request
```bash
curl -G \
  'https://graph.facebook.com/v21.0/act_123456789/insights' \
  -d 'level=ad' \
  -d 'fields=ad_name,impressions,clicks,spend,conversions,cost_per_conversion' \
  -d 'breakdowns=["age","gender"]' \
  -d 'time_range={"since":"2025-03-01","until":"2025-03-17"}' \
  -d 'access_token=YOUR_ACCESS_TOKEN'
```

### Important Note
When using `age` or `gender` breakdowns with `reach`, data is limited to **13 months (394 days)**.

---

## 5. Device Breakdown

### Purpose
Optimize by device type (mobile, desktop, tablet).

### Request Parameters
```json
{
  "level": "ad",
  "fields": [
    "campaign_name",
    "adset_name",
    "ad_name",
    "impressions",
    "clicks",
    "spend",
    "conversions"
  ],
  "breakdowns": ["device_platform", "impression_device"],
  "time_range": {
    "since": "2025-03-01",
    "until": "2025-03-17"
  }
}
```

### Breakdown Values

#### `device_platform`
- `mobile`
- `desktop`

#### `impression_device`
- `desktop`
- `iphone`
- `ipad`
- `android_smartphone`
- `android_tablet`
- `other`

### Example cURL Request
```bash
curl -G \
  'https://graph.facebook.com/v21.0/act_123456789/insights' \
  -d 'level=ad' \
  -d 'fields=ad_name,impressions,clicks,spend,conversions' \
  -d 'breakdowns=["impression_device"]' \
  -d 'time_range={"since":"2025-03-01","until":"2025-03-17"}' \
  -d 'access_token=YOUR_ACCESS_TOKEN'
```

---

## Combining Breakdowns

### Allowed Combinations
You can combine certain breakdowns, but not all. Here are valid combinations:

| Combination | Valid |
|-------------|-------|
| `age` + `gender` | ✅ Yes |
| `publisher_platform` + `platform_position` | ✅ Yes |
| `device_platform` + `impression_device` | ✅ Yes |
| `age` + `publisher_platform` | ✅ Yes |
| `age` + `device_platform` | ✅ Yes |

### Invalid Combinations
- Cannot combine `action_*` breakdowns with most other breakdowns
- Check Meta's documentation for full compatibility matrix

---

## Summary: API Endpoints to Integrate

### Google Ads API
| Data | Resource | Priority |
|------|----------|----------|
| Search Terms | `search_term_view` | 🔴 Critical |
| Quality Score | `keyword_view` + `ad_group_criterion` | 🔴 Critical |
| Keyword Performance | `keyword_view` | 🔴 Critical |
| Impression Share | `campaign` metrics | 🟡 High |
| Device Breakdown | `campaign` + `segments.device` | 🟡 Medium |
| Geographic Data | `geographic_view` | 🟡 Medium |
| PMax Assets | `asset_group_asset` | 🟡 High |

### Meta Ads API
| Data | Fields/Breakdowns | Priority |
|------|-------------------|----------|
| Frequency | `frequency`, `reach` | 🔴 Critical |
| Video Metrics | `video_thruplay_watched_actions`, `video_p*_watched_actions` | 🔴 Critical |
| Placement | `breakdowns: publisher_platform, platform_position` | 🟡 High |
| Demographics | `breakdowns: age, gender` | 🟡 Medium |
| Device | `breakdowns: impression_device` | 🟡 Medium |

---

## Authentication Requirements

### Google Ads API
1. **Developer Token** - Apply at Google Ads API Center
2. **OAuth 2.0 Client ID** - Create in Google Cloud Console
3. **Refresh Token** - Obtained via OAuth flow
4. **Customer ID** - The Google Ads account ID (without dashes)

### Meta Marketing API
1. **App ID** - Create a Meta App
2. **App Secret** - From Meta App settings
3. **Access Token** - With `ads_read` permission
4. **Ad Account ID** - Format: `act_123456789`

---

## Rate Limits

### Google Ads API
- 15,000 requests per day per developer token
- 1,000 requests per 100 seconds per customer ID

### Meta Marketing API
- 200 calls per hour per ad account
- 4,800 calls per day per ad account
- Async reports recommended for large data pulls

---

## Next Steps for Integration

1. **Phase 1 (Critical)**
   - Integrate Google `search_term_view` and `keyword_view`
   - Integrate Meta `frequency`, `reach`, and video metrics

2. **Phase 2 (High Priority)**
   - Add Google impression share metrics
   - Add Meta placement breakdowns

3. **Phase 3 (Enhancement)**
   - Add device and geographic breakdowns
   - Add PMax asset performance
