---
name: meta-creative-analysis
version: 1.0.0
description: Extracts, processes, and analyzes creative performance data for Meta Ads (Facebook/Instagram). Identifies creative fatigue, video engagement drop-offs, and poor conversion drivers using Full Impact Attribution and Meta's rich video metrics.
---
# Skill: AllyClaw Meta Creative Analysis

## 🎯 Attribuly Unique Value Proposition

**Why Attribuly Meta Creative Analysis is superior:**

| Capability | Meta Ads Manager | Attribuly | Why It Matters |
|------------|------------------|-----------|----------------|
| **True ROAS** | Inflated by 1-day view attribution | ✅ Full Impact Attribution | Shows the actual business impact of specific creatives. |
| **New Customer Value** | All conversions look the same | ✅ Tracks `new_order_roas` by ad | Identifies which creatives acquire net-new customers. |
| **Profitability** | Revenue based only | ✅ Includes `profit` and `margin` | Prevents scaling creatives that lose money. |
| **Video Engagement** | Basic metrics only | ✅ Full video funnel (p25-p100) | Identifies where viewers drop off in video ads. |
| **Creative Fatigue** | Manual detection | ✅ Automated fatigue scoring | Proactively flags declining creatives before they bleed budget. |

**Key Insight:** Meta's native reporting inflates creative performance through view-through attribution. This skill uses Full Impact Attribution to find creatives that drive TRUE incremental, profitable growth.

---

## When to Trigger This Skill

### Automatic Triggers
- When Meta Ads CTR drops > 15% week-over-week.
- When Meta Ads CPA increases > 20% week-over-week.
- When video completion rate (p100) drops > 25% for video creatives.
- Triggered as a secondary diagnostic skill by `meta_ads_performance` or `weekly_marketing_performance`.

### Manual Triggers (User Commands)
- "Which Meta ads are performing best?"
- "Analyze our Meta creative performance."
- "Are we experiencing creative fatigue on Meta?"
- "Find the worst performing video ads."
- "Check video engagement rates."
- "Compare Feed vs Stories vs Reels performance."

### Context Triggers
- After launching new creative assets (images, videos, carousels).
- When overall Meta account performance is declining despite stable budgets.
- When video ad spend increases but conversions remain flat.
- When comparing static vs video creative performance.

---

## Skill Purpose

Provide a deep-dive diagnostic report on Meta Ads creative performance:
1. **Performance Ranking** — Rank creatives (ads) by True ROAS, Profit, and ncROAS using Full Impact Attribution.
2. **Video Engagement Analysis** — Analyze video completion funnel (p25 → p50 → p75 → p100) to identify drop-off points.
3. **Fatigue Detection** — Identify high-spend, declining-CTR creatives that need rotation.
4. **Acquisition Quality** — Highlight creatives driving net-new customers vs. repeat buyers.
5. **Actionable Recommendations** — Provide specific instructions to pause, scale, or iterate creatives.

---

## Data Sources

### Primary APIs

#### 1. Get Ad Analysis (Campaign/Ad Set/Ad Level)
**Endpoint:** `POST /{version}/api/get/ad-analysis/list`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Fetch granular, ad-level performance metrics, combining platform data (impressions, clicks) with Attribuly's Full Impact Attribution conversion data.

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | string | Yes | Start date (`YYYY-MM-DD`) |
| `end_date` | string | Yes | End date (`YYYY-MM-DD`) |
| `dimensions` | array[string] | Yes | `["channel", "campaign", "ad_set", "ad_name"]` |
| `filters` | array[object] | Yes | Filter for `channel IN ['meta', 'facebook', 'instagram']` |
| `model` | string | Yes | Attribution model (default: `full_impact`) |

**Primary Fields Used**

| Field | Description |
|-------|-------------|
| `ad_name` | Name/Identifier of the creative |
| `impressions` | Platform impressions |
| `clicks` | Platform clicks |
| `ctr` | Click-through rate |
| `spend` | Ad spend |
| `conversions` | Full Impact attributed purchases |
| `roas` | Full Impact Return on Ad Spend |
| `new_order_roas` | ROAS from new customers |
| `profit` | Calculated profit (Revenue - Spend - COGS) |
| `margin` | Profit margin % |

#### 2. Meta Query API (Supplemental Video & Engagement Data)
**Endpoint:** `POST /{version}/api/source/meta-query`  
**Base URL:** `https://data.api.attribuly.com`  
**Authentication:** `ApiKey` header  
**Purpose:** Fetch Meta-native video engagement metrics and creative-level insights via Meta Marketing API.

**Required Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `account_id` | string | Yes | Meta Ads account ID (obtain from Connected Sources API) |
| `path` | string | Yes | Meta API path (e.g., `act_{account_id}/insights`) |
| `params.level` | string | Yes | Query level: `ad` (for creative analysis) |
| `params.fields` | array[string] | Yes | Fields to retrieve (see Available Fields table) |
| `params.time_range` | object | Yes | Time range with `since` and `until` |

**Available Fields for Creative Analysis:**

| Field Category | Fields | Purpose |
|----------------|--------|---------|
| **Basic Metrics** | `ad_name`, `ad_id`, `impressions`, `reach`, `frequency` | Creative identification, reach, and frequency |
| **Engagement Metrics** | `clicks`, `ctr`, `cpc`, `spend`, `conversions`, `cpa` | Performance and efficiency metrics |
| **Video Engagement** | `video_play_actions`, `video_p25/p50/p75/p95/p100_watched_actions` | Video completion funnel |
| **Video Quality** | `video_thruplay_watched_actions`, `video_avg_time_watched_actions` | Video engagement quality |
| **Short Engagement** | `video_continuous_2_sec_watched_actions`, `video_30_sec_watched_actions` | Initial hook effectiveness |
| **Creative Format** | `creative_type`, `call_to_action_type`, `object_story_id` | Creative format identification (image/video/carousel) |
| **Placement Performance** | `placement_platform`, `placement_position`, `publisher_platform` | Where the ad appeared (Feed/Stories/Reels) |
| **Device Breakdown** | `device_platform`, `impression_device` | Performance by device (mobile/desktop) |
| **Demographic Insights** | `age`, `gender`, `country` | Audience breakdown for creative resonance |
| **Conversion Metrics** | `actions`, `action_values`, `cost_per_action_type` | Detailed conversion tracking |
| **Quality Rankings** | `quality_ranking`, `engagement_rate_ranking`, `conversion_rate_ranking` | Meta's internal quality scores |
| **Social Proof** | `social_spend`, `inline_link_clicks`, `post_engagement` | Social engagement signals |

**Key Video Engagement Metrics:**

| Metric | Field Name | Why It Matters |
|--------|------------|----------------|
| **Video Plays** | `video_play_actions` | Total video starts |
| **25% Completion** | `video_p25_watched_actions` | Initial hook effectiveness |
| **50% Completion** | `video_p50_watched_actions` | Mid-video engagement |
| **75% Completion** | `video_p75_watched_actions` | Strong interest signal |
| **100% Completion** | `video_p100_watched_actions` | Full message delivery |
| **ThruPlay** | `video_thruplay_watched_actions` | Meta's quality metric (15s or full) |
| **Avg Watch Time** | `video_avg_time_watched_actions` | Average engagement duration |
| **2-Second Continuous** | `video_continuous_2_sec_watched_actions` | Initial attention capture |
| **30-Second Watch** | `video_30_sec_watched_actions` | Meaningful engagement threshold |

**Key Placement & Format Metrics:**

| Metric | Field Name | Why It Matters |
|--------|------------|----------------|
| **Placement Platform** | `placement_platform` | Facebook vs Instagram vs Audience Network |
| **Placement Position** | `placement_position` | Feed vs Stories vs Reels vs Right Column |
| **Publisher Platform** | `publisher_platform` | Specific publisher (Facebook, Instagram, Messenger) |
| **Creative Type** | `creative_type` | IMAGE, VIDEO, CAROUSEL, COLLECTION |
| **CTA Type** | `call_to_action_type` | SHOP_NOW, LEARN_MORE, SIGN_UP, etc. |
| **Frequency** | `frequency` | Average times user saw the ad (fatigue indicator) |
| **Quality Ranking** | `quality_ranking` | Meta's quality score (ABOVE_AVERAGE, AVERAGE, BELOW_AVERAGE) |
| **Engagement Rate Ranking** | `engagement_rate_ranking` | Relative engagement performance |
| **Conversion Rate Ranking** | `conversion_rate_ranking` | Relative conversion performance |
| **Device Platform** | `device_platform` | Mobile vs Desktop performance |
| **Age/Gender Breakdown** | `age`, `gender` | Demographic resonance analysis |

**Why Placement Analysis Matters:**
- **Feed vs Stories vs Reels:** Different placements require different creative formats and aspect ratios
- **Facebook vs Instagram:** Different audience behaviors and engagement patterns
- **Mobile vs Desktop:** Mobile-first creative design is critical for Meta
- **Frequency > 3:** Indicates creative fatigue, needs rotation

**Why Quality Rankings Matter:**
- Meta uses these rankings to determine ad delivery and costs
- BELOW_AVERAGE rankings lead to higher CPMs and lower reach
- Use rankings to identify creatives that need improvement before they fatigue

---

## Default Parameters

| Parameter | Default Value | Notes |
|-----------|---------------|-------|
| `version` | `v2-4-2` | API version |
| `start_date` | Today - 14 days | Standard creative evaluation window |
| `end_date` | Today - 1 day | Yesterday, explicitly excluding today |
| `model` | `full_impact` | Full Impact Attribution (default for Meta) |
| `page_size` | `200` | Higher limit due to Meta's larger creative volume |
| `attribution_window` | `30d_click_1d_view` | Standard Attribuly Full Impact attribution window |

---

## Execution Steps

### Step 1: Validate Input
- Ensure `start_date <= end_date`.
- Ensure integration with Meta Ads is active via the Settings API.
- Validate `model` is set to `full_impact` for accurate attribution.

### Step 2: Fetch Ad-Level Data
- Call `/api/get/ad-analysis/list` with dimensions `["channel", "campaign", "ad_set", "ad_name"]`.
- Filter for `channel IN ['meta', 'facebook', 'instagram']`.
- Use `model = 'full_impact'` for Full Impact Attribution.

### Step 3: Fetch Supplemental Meta Video Data
- First, retrieve the connected Meta Ads account ID via `POST /{version}/api/get/connection/source` with `platform_type: "meta"`.
- Execute Meta Query API call to `/api/source/meta-query` with:
  - `level: "ad"`
  - `fields`: Include all video engagement metrics
  - `time_range`: Same as Step 2 date range
- Parse video completion funnel: p25 → p50 → p75 → p100

### Step 4: Implement Caching (If Applicable)
- Cache the response for 1 hour to prevent rate-limiting on repeated granular queries during the same session.
- Meta Query API has stricter rate limits than standard Attribuly APIs.

### Step 5: Analyze and Categorize Creatives
Sort and categorize the `records` into distinct buckets:

**Top Performers (Scale):**
- High Spend, ROAS > Target, Profit > 0
- Video completion rate (p100/p25) > 20%
- ncROAS > 1.5x (strong new customer acquisition)

**Fatigued (Refresh):**
- High Spend, declining CTR vs historical average (>15% drop)
- Video completion rate declining > 25%
- ROAS declining but still profitable

**Bleeders (Pause):**
- High Spend, Profit < 0
- Low ncROAS (< 0.8x)
- Video completion rate < 10% (poor engagement)

**Hidden Gems (Test):**
- Low Spend, High CTR (> 2%)
- High ncROAS (> 2.0x)
- Strong video completion rate (> 25%)

**Video-Specific Categories:**
- **Strong Hook, Weak Finish:** High p25, low p100 → Improve mid-video content
- **Weak Hook:** Low p25 → Improve first 3 seconds
- **Strong Finish:** High p100/p25 ratio → Message resonates, scale this creative

### Step 6: Generate Recommendations & A/B Testing Protocols
- Formulate specific actions for the categorized creatives.
- If recommending a new test, enforce A/B testing protocols:
  - Isolate one variable (creative, copy, or audience)
  - Ensure minimum 10,000 impressions / 50 conversions
  - Run for at least 7 days before declaring a winner based on True ROAS
  - Use Full Impact Attribution for final decision

---

## Key Metrics to Analyze

| Metric | Field Name | Why It Matters | Benchmark |
|--------|------------|----------------|-----------|
| **Engagement** | `ctr` | Indicates initial creative resonance | > 1.5% for Feed, > 0.8% for Stories |
| **Video Hook** | `video_continuous_2_sec_watched_actions / video_play_actions` | First 2-second retention rate | > 60% |
| **Video Completion** | `video_p100_watched_actions / video_play_actions` | Full message delivery rate | > 15% |
| **Video Drop-off** | `(video_p25 - video_p50) / video_p25` | Mid-video engagement loss | < 30% |
| **Efficiency** | `roas`, `cpa` | Determines if creative converts profitably | ROAS > 2.5x |
| **Scale** | `spend`, `impressions` | Shows how much the platform trusts the creative | Varies by budget |
| **Incrementality** | `new_order_roas` | Proves creative is generating net-new business | ncROAS > 1.5x |
| **Bottom Line** | `profit` | Ultimate measure of creative success | Profit > $0 |
| **Reach Efficiency** | `reach / impressions` | Frequency indicator (lower = broader reach) | 0.6-0.8 optimal |
| **Frequency** | `frequency` | Ad fatigue indicator | < 3.0 (warning), > 4.0 (critical) |
| **Quality Ranking** | `quality_ranking` | Meta's internal quality score | ABOVE_AVERAGE |
| **Placement Perf** | `placement_position` | Performance by placement (Feed/Stories/Reels) | Varies by format |
| **Device Perf** | `device_platform` | Mobile vs Desktop performance | Mobile typically 70-80% of traffic |
| **Creative Type** | `creative_type` | IMAGE vs VIDEO vs CAROUSEL performance | Video typically outperforms static |

---

## Standardized Creative Evaluation Rubric

When qualitative review of a Meta ad is required, evaluate it using this 1-5 point rubric:

**For Static/Carousel Ads:**
- **Visual Hierarchy:** (1) Cluttered/Illegible → (5) Single clear focal point.
- **Messaging Clarity:** (1) Vague/Jargon → (5) Clear value proposition addressing pain points.
- **Brand Alignment:** (1) Inconsistent tone → (5) Instantly recognizable brand voice.
- **CTA Strength:** (1) Passive (e.g., "Learn More") → (5) Action-oriented with urgency (e.g., "Shop Now - 20% Off").

**For Video Ads:**
- **Hook Strength (0-3s):** (1) Slow start → (5) Immediate attention grabber.
- **Story Flow:** (1) Disjointed → (5) Clear narrative arc.
- **Sound-Off Optimization:** (1) Requires sound → (5) Fully understandable without sound.
- **Mobile-First Design:** (1) Desktop-oriented → (5) Optimized for vertical/mobile viewing.

---

## Root Cause Analysis Logic

### Scenario 1: High CTR, Low ROAS
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `ctr` > 2%, `roas` < 1.5 | Clickbait ad copy, irrelevant landing page, or poor pricing fit. | Review landing page experience. Ensure ad promise matches LP. Check if audience is too broad. |

### Scenario 2: Low CTR, High ROAS
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `ctr` < 0.8%, `roas` > 3.0 | Highly qualified but narrow audience. Creative is filtering out bad clicks. | Do not pause. Try iterating variations of this creative to slightly broaden appeal without losing quality. |

### Scenario 3: Creative Fatigue
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| High `impressions`, `ctr` dropping > 15% over time | Audience exhaustion. | Pause and replace with fresh visual assets or updated copy hooks. Consider rotating to new audience segments. |

### Scenario 4: Video Hook Failure
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `video_p25 / video_play_actions` < 40% | Weak first 3 seconds. Viewers dropping off immediately. | Redesign video hook. Add text overlay, motion, or strong visual in first 2 seconds. Test different opening scenes. |

### Scenario 5: Video Mid-Point Drop-off
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `(video_p50 - video_p25) / video_p25` > 40% | Content loses momentum after initial hook. | Tighten mid-video content. Add value proposition earlier. Reduce filler content between hook and CTA. |

### Scenario 6: High Completion, Low Conversion
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `video_p100 / video_play_actions` > 20%, but `roas` < 1.5 | Strong creative but weak offer or landing page mismatch. | Creative is working. Fix landing page, pricing, or offer. Ensure LP matches video promise. |

### Scenario 7: Placement-Specific Underperformance
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| High performance in Feed, low in Stories/Reels | Creative not optimized for vertical format or short attention span. | Create 9:16 vertical versions for Stories/Reels. Shorten hook to 1-2 seconds. Add text overlays for sound-off viewing. |

### Scenario 8: High Frequency, Declining Performance
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `frequency` > 3.0, `ctr` declining > 20% | Audience saturation and creative fatigue. | Pause creative for 7-14 days. Rotate to new audience segment. Refresh creative with new visuals or messaging. |

### Scenario 9: Quality Ranking Below Average
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| `quality_ranking` = BELOW_AVERAGE | Meta's algorithm penalizing the creative (poor UX, misleading, low engagement). | Review creative for policy compliance. Improve engagement signals (hook, CTA). Test new creative variations. |

### Scenario 10: Device-Specific Performance Gap
| Condition | Likely Cause | Action |
|-----------|--------------|--------|
| Strong desktop performance, weak mobile (or vice versa) | Creative not optimized for the underperforming device. | For mobile: Use vertical format, larger text, faster load times. For desktop: Use landscape format, more detailed copy. |

---

## Expected Output (Consolidated Dashboard)

```markdown
# 🎨 Meta Creative Analysis Dashboard
Date Range: [start] to [end]
Attribution Model: Full Impact Attribution

## 1) Executive Overview
- **Total Meta Ad Spend Analyzed:** $[X]
- **Blended ROAS (Full Impact):** [X.Xx]
- **Primary Insight:** [e.g., Video assets in Campaign Y are driving 80% of new customer revenue, while static images are bleeding profit.]

## 2) The Winners Circle (Scale List)
| Ad Name | Spend | CTR | Video Completion | True ROAS | ncROAS | Profit |
|---------|------:|----:|-----------------:|----------:|-------:|-------:|
| Video_Ad_A | $800 | 2.1% | 22% | 3.8x | 2.9x | +$2,200 |
| Carousel_Ad_B | $600 | 1.8% | N/A | 3.2x | 2.5x | +$1,400 |
*Action: Scale budget slowly (15-20% every 2-3 days).*

## 3) The Fatigue Warning (Refresh List)
| Ad Name | Spend | CTR | Historical CTR | Video Completion | True ROAS | Profit |
|---------|------:|----:|---------------:|-----------------:|----------:|-------:|
| Video_Ad_C | $700 | 1.2% | 2.5% (Peak) | 15% (was 28%) | 1.9x | +$200 🟡 |
*Action: Rotate creative or pause. Video completion dropped 46%.*

## 4) The Bleeders (Pause List)
| Ad Name | Spend | CTR | Video Completion | True ROAS | ncROAS | Profit |
|---------|------:|----:|-----------------:|----------:|-------:|-------:|
| Static_Ad_D | $900 | 0.6% | N/A | 0.9x | 0.3x | -$500 🔴 |
*Action: Immediate pause to stop profit bleed.*

## 5) Video Engagement Funnel Analysis
| Ad Name | Plays | p25 | p50 | p75 | p100 | Drop-off Point |
|---------|------:|----:|----:|----:|-----:|----------------|
| Video_Ad_A | 10,000 | 65% | 45% | 30% | 22% | Healthy funnel ✅ |
| Video_Ad_C | 8,000 | 35% | 18% | 10% | 8% | Weak hook 🔴 |
| Video_Ad_E | 12,000 | 70% | 25% | 12% | 10% | Mid-video drop 🟡 |

**Insights:**
- Video_Ad_A: Strong hook and retention. Scale this creative.
- Video_Ad_C: Weak first 3 seconds. Redesign hook with stronger visual/text overlay.
- Video_Ad_E: Strong hook but loses viewers at 25-50% mark. Tighten mid-video content.

## 6) Strategic Recommendations & DTC Best Practices
1. **Video Hook Optimization:** 3 out of 5 video ads have p25 < 40%. Redesign first 3 seconds with text overlay and motion.
2. **Creative Mix:** Static ads are underperforming (avg ROAS 1.2x). Shift 60% of budget to video and carousel formats.
3. **Audience Strategy:** Move `Video_Ad_A` strictly to Prospecting (Top of Funnel) due to its high `ncROAS` (2.9x).
4. **Seasonal Trend:** Pre-load Q4 creatives 3-4 weeks early to exit the learning phase before peak CPAs hit.
5. **Testing Protocol:** Launch A/B test for Video_Ad_C with 3 different hooks. Run for 7 days, minimum 10k impressions per variant.
```

---

## Error Handling & Data Validation

- **Rate Limit (`429`)**: Apply exponential backoff (1s, 2s, 4s). Meta Query API has stricter limits.
- **Empty Results**: If `data.records.length === 0`, output: "No Meta Ads creative data found for the selected date range. Verify active campaigns and date selection."
- **Missing Dimensions**: Ensure `ad_name` is present. If missing, fall back to analyzing at the `ad_set` level and flag the missing data.
- **Data Validation**: Ensure `spend` > 0 before calculating efficiency ratios to prevent division by zero.
- **Video Metrics Missing**: If video fields are empty for a video ad, flag as "Video tracking not configured" and recommend checking Meta pixel setup.

---

## Thresholds for Alerts

| Metric | Warning (🟡) | Critical (🔴) |
|--------|--------------|---------------|
| `ctr` | < 1.0% | < 0.6% |
| `roas` | < Target ROAS | < 1.0 (Losing money) |
| `profit` | < $50 | < $0 |
| `video_p25 / video_play_actions` | < 40% | < 25% |
| `video_p100 / video_play_actions` | < 15% | < 8% |
| `new_order_roas` | < 1.2x | < 0.8x |
| `frequency` | > 3.0 | > 4.0 |
| `quality_ranking` | AVERAGE | BELOW_AVERAGE |
| `engagement_rate_ranking` | AVERAGE | BELOW_AVERAGE |
| `conversion_rate_ranking` | AVERAGE | BELOW_AVERAGE |

---

## Example API Calls

### Fetch Granular Ad Performance for Meta
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/get/ad-analysis/list" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-03-01",
    "end_date": "2026-03-17",
    "dimensions": ["channel", "campaign", "ad_set", "ad_name"],
    "filters": [
      {
        "key": "channel",
        "operator": "in",
        "value": ["meta", "facebook", "instagram"]
      }
    ],
    "model": "full_impact",
    "goal": "purchase",
    "orders": [{"column": "spend", "order": "desc"}],
    "page": 1,
    "page_size": 200
  }'
```

### Fetch Meta Video Engagement Data
```bash
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/source/meta-query" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "1040634550541",
    "path": "act_1040634550541/insights",
    "params": {
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
            "video_p100_watched_actions",
            "video_thruplay_watched_actions",
            "video_avg_time_watched_actions"
        ],
        "time_range": {
            "since": "2026-03-01",
            "until": "2026-03-17"
        }
    }
  }'
```

---

## Dependencies

| Dependency | Type | Purpose |
|------------|------|---------|
| Attribuly Data API | External API | Fetch granular ad-level metrics with Full Impact Attribution |
| Attribuly Meta Query API | External API | Execute Meta Marketing API queries for video engagement metrics |
| Caching Module | System | Temporarily store high-volume ad data to optimize performance |
| Connected Sources API | External API | Retrieve Meta Ads account ID for query API calls |

---

## Related Skills

| Skill | When to Trigger |
|-------|-----------------|
| `meta_ads_performance` | Parent skill; triggers creative analysis when campaign efficiency drops. |
| `creative_fatigue_detector` | Triggers automatically when specific frequency/CTR thresholds are crossed across multiple platforms. |
| `landing_page_analysis` | Triggered when high CTR creatives result in low ROAS (indicating a post-click drop-off). |
| `google_creative_analysis` | For cross-platform creative comparison (Meta vs Google). |
