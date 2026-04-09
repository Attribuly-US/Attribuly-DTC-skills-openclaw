**English** | [简体中文](./README.zh-CN.md) | [日本語](./README.ja.md)

# 🛍️ Attribuly OpenClaw Skills: AI Marketing Analytics for Shopify & WooCommerce

[!\[Deploy to Attribuly Cloud\](https://img.shields.io/badge/Deploy%20to-Attribuly%20Cloud-blue?style=for-the-badge null)](https://app.attribuly.com/cherry/#/ally-claw)

[Watch the video](https://youtu.be/JDat6bls5Wk?si=jgZtnRZnl39VqFj3)

> **The Problem:** Meta says you made $10,000. Google claims another $8,000. But your Shopify dashboard only shows $12,000 total. They are both taking credit for the same sales, leaving you blind to your true ROAS and actual profit margins.
>
> **The Solution:** Meet your new "AI Marketing Analyst"—free, open-source, and tireless. It plugs directly into your first-party backend data, cuts through the ad platform noise, and tells you exactly where your budget is bleeding.
>
> **Take Action:** Developer teams can clone the repo and run it locally for free. Want it running in 60 seconds with zero configuration? Try our fully-managed Cloud Version with a 50% discount on your first month.

Your specialized **AI Marketing Partner for DTC Ecommerce (Shopify, WooCommerce, and more)**. Powered by Attribuly's first-party data, these OpenClaw skills provide autonomous marketing analysis, true ROAS tracking, and profit-first optimization for your online store.

## 🚀 Get Started / 快速开始 / はじめに

**Get started:** Sign up and get your API key at <https://attribuly.com> (14-day free trial available).\
**快速开始:** 请前往 <https://attribuly.com> 注册获取 API Key（提供 14 天免费试用）。\
**はじめに:** <https://attribuly.com> でサインアップして API Key を取得してください（14日間の無料トライアルあり）。

### Why for Shopify & WooCommerce?

Traditional ad platforms (Meta, Google) often misattribute sales. For Shopify and WooCommerce merchants, these skills use your store's real backend data to reveal your **true profit margin, customer acquisition cost (CAC), and lifetime value (LTV)**, ensuring your marketing decisions are driven by actual revenue.

### Core Capabilities:

<div align="center">
  <img src="./assets/weekly-report-workflow.jpeg" width="600" alt="Skill Workflow" />
</div>

- **True ROI & ROAS Focus** — Powered by Attribuly first-party attribution concepts (true ROAS, ncROAS, profit, margin, LTV, MER) to reduce Meta/Google over-attribution.
- **Under Your Control** — Deploy locally or in the cloud. Memory and strategy remain within your secure environment.
- **Extensible Skills** — Built-in automated triggers. Autonomously analyze funnels, pacing, creatives, and discrepancies. No lock-in.

### What you can do:

- **Diagnostic:** Autonomously detect funnel bottlenecks and landing page friction.
- **Performance:** Generate 30-second daily pacing scans or deep-dive weekly executive summaries.
- **Creative:** Evaluate Google/Meta creatives against true profitability and identify fatigue.
- **Optimization:** Get profit-first budget reallocation and audience tuning recommendations.

## 💬 Common Prompts / 常见触发词 / よくあるトリガー

Try asking the agent any of the following to trigger specific skills:

**English:**

- "How did we do last week? Generate a weekly report."
- "How's Google Ads doing?"
- "Where are users dropping off in the funnel?"
- "Where should I shift my spend? Optimize budget."
- "Analyze Google creatives and check for CTR issues."

**中文 (Chinese):**

- "上周表现如何？生成每周报告。"
- "Google广告表现如何？"
- "用户在转化漏斗的哪里流失了？"
- "我应该把预算转移到哪里？优化一下预算。"
- "分析Google素材并检查点击率问题。"

**日本語 (Japanese):**

- "先週のパフォーマンスはどうだった？週次レポートを作成して。"
- "Google広告の調子はどう？"
- "ユーザーはファネルのどこで離脱している？"
- "どこに予算を移すべき？予算を最適化して。"
- "Googleクリエイティブを分析してCTRの課題を確認して。"

***

## Table of Contents

- [Available Skills](#available-skills)
- [Installation Guide](#installation-guide)
- [Managed Cloud Hosting (Deployment)](#managed-cloud-hosting-deployment)
- [Post-Installation](#post-installation)

***

## Available Skills

### ✅ Ready (Available Now)

- `weekly-marketing-performance` — Cross-channel weekly executive summary
- `daily-marketing-pulse` — Daily anomaly detection & pacing (30-sec scan)
- `google-ads-performance` — Deep dive into Google Ads / PMax efficiency
- `meta-ads-performance` — Deep dive into Meta Ads (bridge iOS14 data gap)
- `budget-optimization` — Profit-first budget reallocation rules
- `audience-optimization` — Audience overlap and acquisition/retargeting split
- `bid-strategy-optimization` — tCPA/tROAS targeting based on first-party data
- `funnel-analysis` — End-to-end customer journey drop-off diagnosis
- `landing-page-analysis` — Isolate traffic quality vs UX friction on landing pages
- `attribution-discrepancy` — Quantify and diagnose reporting gaps between ad networks and backend
- `google-creative-analysis` — Integrate Quality Score, PMax assets, and standardized evaluation rubrics for Google Ads

### 🔜 Coming Soon (Planned)

- `tiktok-ads-performance`
- `meta-creative-analysis`
- `creative-fatigue-detector`
- `product-performance`
- `customer-journey-analysis`
- `ltv-analysis`

See the Technical Reference section below for detailed triggers and usage mapping.

\---\*\*\*

## Installation Guide

### 🚀 No-Code Setup for Shopify & WooCommerce Users

Not a developer? No problem! You can run these AI skills without writing any code:

1. Connect your Shopify or WooCommerce store to [Attribuly](https://attribuly.com).
2. Grab your API Key from the Attribuly dashboard.
3. Paste the key into your Agent Settings
4. Ask the AI: *"Analyze my Shopify funnel drop-off for the last 7 days."*

### Step 1: Obtain Your Attribuly API Key

Before installing the skills, you need an Attribuly API key. These skills rely heavily on Attribuly-exclusive metrics (like `new_order_roas` and true profit) to function autonomously.

- **Paid Feature:** The API key is exclusively available to paid-plan users. You must upgrade your workspace before you can generate the key.
- **Free Trial:** If you are new, you can start a [14-day free trial](https://attribuly.com/pricing/) to test the platform.
- **How to get your API key:**
  1. Go to <https://attribuly.com> and sign up
  2. After signing in, navigate to Settings → API Keys
  3. Copy your API key (it should look like `att_xxxxxxxxxxxx`)

***

## API Key Configuration

After obtaining your API key, you need to configure it so the skills can access Attribuly's API. Choose the method that best fits your deployment:

### Method 1: OpenClaw Config (Recommended for Cloud Deployments)

This is the recommended method for Ubuntu servers, Docker containers, and other cloud deployments.

#### Step 1: Set the API Key

Run this command in your terminal (replace `{KEY}` with your actual API key):

```bash
openclaw config set skills.entries.attribuly-dtc-analyst.env.ATTRIBULY_API_KEY "att_your_actual_key"
```

This command writes the API key to the OpenClaw configuration file (typically `~/.openclaw/openclaw.json`).

#### Step 2: Restart the Gateway

**Important:** The gateway must be restarted to load the new configuration:

```bash
openclaw gateway restart
```

Wait 10-15 seconds for the gateway to fully restart.

#### Step 3: Verify the Configuration

```bash
openclaw config get skills.entries.attribuly-dtc-analyst.env.ATTRIBULY_API_KEY
```

Expected output: `att_your_actual_key`

### Method 2: Environment Variable (For Local/Manual Setup)

For local development or manual setups, you can set the environment variable directly.

#### Temporary (Current Session Only)

```bash
export ATTRIBULY_API_KEY="att_your_actual_key"
```

#### Permanent (Ubuntu/Debian)

Add to your shell profile:

```bash
echo 'export ATTRIBULY_API_KEY="att_your_actual_key"' >> ~/.bashrc
source ~/.bashrc
```

Or for zsh:

```bash
echo 'export ATTRIBULY_API_KEY="att_your_actual_key"' >> ~/.zshrc
source ~/.zshrc
```

**Note:** For systemd services or Docker containers, you'll need to set the environment variable in the service configuration or Dockerfile.

### Method 3: Docker Deployment

If running OpenClaw in Docker, set the environment variable in your configuration:

#### Using docker-compose.yml

```yaml
services:
  openclaw:
    image: openclaw/openclaw:latest
    environment:
      - ATTRIBULY_API_KEY=att_your_actual_key
    # ... other config
```

#### Using docker run

```bash
docker run -e ATTRIBULY_API_KEY=att_your_actual_key openclaw/openclaw:latest
```

### Verification

After configuring the API key, verify it's working:

```bash
# Check if the environment variable is set
[ -n "$ATTRIBULY_API_KEY" ] && echo "API key is set" || echo "API key is missing"

# Test the API key with a simple API call
curl -X POST "https://data.api.attribuly.com/v2-4-2/api/all-attribution/get-list-sum" \
  -H "ApiKey: $ATTRIBULY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"start_date": "2025-01-01", "end_date": "2025-01-07", "dimensions": ["channel"], "model": "linear", "goal": "purchase"}'
```

### Troubleshooting

#### Issue: "ATTRIBULY_API_KEY environment variable is still not set"

**Cause:** The gateway hasn't been restarted after setting the config.

**Solution:**
1. Verify the config is saved:
   ```bash
   cat ~/.openclaw/openclaw.json | grep -A 5 "attribuly-dtc-analyst"
   ```
2. If the config is missing, re-run the set command
3. Restart the gateway:
   ```bash
   openclaw gateway restart
   ```
4. Wait 10-15 seconds for the gateway to fully restart
5. Try your query again

#### Issue: API calls fail with authentication error

**Cause:** The API key might be incorrect or expired.

**Solution:**
1. Verify your API key in the Attribuly dashboard
2. Re-set the config with the correct key
3. Restart the gateway

#### Issue: Gateway won't restart

**Cause:** The gateway process might be stuck or there's a configuration syntax error.

**Solution:**
1. Check gateway status:
   ```bash
   openclaw gateway status
   ```
2. If stuck, force kill and restart:
   ```bash
   pkill -f openclaw
   openclaw gateway start
   ```
3. Check logs for errors:
   ```bash
   openclaw gateway logs
   ```

***

There are two primary ways to install these Attribuly skills into your own OpenClaw environment. Choose the method that best fits your workflow.

### Step 2: Install via chat (Quick Start)

Copy the prompt below into your OpenClaw interface, and the agent will install it for you:

> Install these skills from <https://github.com/Attribuly-US/ecommerce-dtc-skills>

# Managed Cloud Hosting (Deployment)

| Feature               | Local Deployment (Open Source)                            | Cloud Version (Attribuly Managed)                                                |
| :-------------------- | :-------------------------------------------------------- | :------------------------------------------------------------------------------- |
| **Best For**          | Geek teams, Developers                                    | DTC Merchants, Marketers, Non-technical teams                                    |
| **Cost**              | Free                                                      | $20/month (50% off first month)                                                  |
| **Setup**             | Requires self-hosting, API config & environment tinkering | 1-minute out-of-the-box, Zero code required                                      |
| **Maintenance**       | Manual updates, Self-maintained                           | Auto-updates, Zero maintenance                                                   |
| **Core Advantage**    | Basic LLM invocation                                      | Intelligent multi-LLM orchestration, Operational management, Enterprise security |
| **Advanced Features** | None                                                      | Organizational Long-term Memory (Coming soon)                                    |

If you want to eliminate technical hassles and focus entirely on your marketing data, we highly recommend clicking the **Deploy to Attribuly Cloud** button at the top to start your cloud trial.

## Post-Installation

Once the skill bundle is successfully placed in your `openclaw-config/skills/` directory (locally or in the cloud), refer to the **Technical Reference** section below for details on specific triggers, skill chaining logic, and global API parameters.

***

## Technical Reference

### Skill Trigger Matrix

#### Automatic Triggers

| Condition              | Triggered Skill                                     | Priority |
| :--------------------- | :-------------------------------------------------- | :------- |
| Monday 09:00 AM        | `weekly-marketing-performance`                      | High     |
| Daily 09:00 AM         | `daily-marketing-pulse`                             | Medium   |
| ROAS drops >20%        | `weekly-marketing-performance` + channel drill-down | Critical |
| CPA increases >20%     | Channel-specific performance skill                  | High     |
| CTR drops >15%         | `creative-fatigue-detector`                         | Medium   |
| CVR drops >15%         | `funnel-analysis`                                   | High     |
| Spend >30% over budget | `budget-optimization`                               | Critical |

### Skill Chaining Logic

When one skill detects an issue, it can trigger related skills:

```text
weekly-marketing-performance
├── IF Google Ads issue detected → google-ads-performance
│   └── IF CTR issue → google-creative-analysis
├── IF Meta Ads issue detected → meta-ads-performance
│   └── IF frequency high → meta-creative-analysis
├── IF CVR issue detected → funnel-analysis
│   └── IF landing page issue → landing-page-analysis
└── IF budget inefficiency → budget-optimization
```

### Global API Parameters

These defaults apply to ALL skills unless overridden:

| Parameter   | Default Value | Notes                                                          |
| :---------- | :------------ | :------------------------------------------------------------- |
| `model`     | `linear`      | Linear attribution                                             |
| `goal`      | `purchase`    | Purchase conversions (use dynamic goal code from Settings API) |
| `version`   | `v2-4-2`      | API version                                                    |
| `page_size` | `100`         | Max records per page                                           |

**Base URL:** `https://data.api.attribuly.com`
**Authentication:** `ApiKey` header (Read from `ATTRIBULY_API_KEY` Environment Variable / Secret Manager. NEVER ask the user for this in chat.)

### Decision Framework: Compare Platform vs. Attribuly Metrics

| Scenario       | Platform ROAS | Attribuly ROAS | Diagnosis                                            | Action                                                    |
| :------------- | :------------ | :------------- | :--------------------------------------------------- | :-------------------------------------------------------- |
| Hidden Gem     | Low (<1.5)    | High (>2.5)    | Top-of-funnel driver undervalued by platform         | **DO NOT PAUSE.** Tag as "TOFU Driver." Consider scaling. |
| Hollow Victory | High (>3.0)   | Low (<1.5)     | Platform over-attributing (likely brand/retargeting) | **CAP BUDGET.** Investigate incrementality.               |
| True Winner    | High (>2.5)   | High (>2.5)    | Genuine high performer                               | **SCALE.** Increase budget 20% every 3-5 days.            |
| True Loser     | Low (<1.0)    | Low (<1.0)     | Inefficient spend                                    | **PAUSE or REDUCE.** Refresh creative or audience.        |

### Key Metrics Glossary

| Metric         | Formula                                          | Description                          |
| :------------- | :----------------------------------------------- | :----------------------------------- |
| **ROAS**       | `conversion_value / spend`                       | Attribuly-tracked Return on Ad Spend |
| **ncROAS**     | `ncPurchase / spend`                             | New Customer ROAS                    |
| **MER**        | `total_revenue / total_spend`                    | Marketing Efficiency Ratio           |
| **CPA**        | `spend / conversions`                            | Cost Per Acquisition                 |
| **CPC**        | `spend / clicks`                                 | Cost Per Click                       |
| **CPM**        | `(spend / impressions) * 1000`                   | Cost Per 1000 Impressions            |
| **CTR**        | `(clicks / impressions) * 100%`                  | Click-Through Rate                   |
| **CVR**        | `(conversions / clicks) * 100%`                  | Conversion Rate                      |
| **LTV**        | `total_sales / unique_customers`                 | Lifetime Value                       |
| **Net Profit** | `sales - shipping - spend - COGS - taxes - fees` | True Profit                          |
| **Net Margin** | `net_profit / sales * 100%`                      | Profit Margin                        |

