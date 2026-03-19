# AllyClaw OpenClaw Skills (Attribuly) for DTC Ecommerce

This folder contains AllyClaw skill documents for OpenClaw-based AI marketing workflows, designed for DTC ecommerce teams running Shopify and WooCommerce stores. The skills are powered by Attribuly first-party attribution concepts (true ROAS, ncROAS, profit, margin, LTV, MER) to reduce platform over-attribution and improve profitable growth.

## Included Skills

### Ready (Available Now)
- `weekly_marketing_performance` — Weekly executive summary across channels
- `daily_marketing_pulse` — Daily anomaly + pacing report (30-second scan)
- `google_ads_performance` — Google Ads / PMax performance diagnosis
- `meta_ads_performance` — Meta Ads performance diagnosis (iOS14 gap-aware)
- `budget_optimization` — Profit-first budget reallocation recommendations
- `audience_optimization` — Cannibalization + prospecting/retargeting audience tuning
- `bid_strategy_optimization` — tCPA/tROAS target setting using first-party truth

### Coming Soon (Planned)
- `tiktok_ads_performance`
- `google_creative_analysis`
- `meta_creative_analysis`
- `creative_fatigue_detector`
- `funnel_analysis`
- `landing_page_analysis`
- `attribution_discrepancy`
- `product_performance`
- `customer_journey_analysis`
- `ltv_analysis`

See [SKILL_REGISTRY.md](file:///Users/alex/Documents/trae_projects/attribuly/outreach-performance/openclaw-config/skills/SKILL_REGISTRY.md) for triggers and usage mapping.

## Install Into Your OpenClaw Instance

These skills are stored in this GitHub repo under `openclaw-config/skills/`. To install them into your own OpenClaw instance, copy the `.md` skill files into your instance’s `openclaw-config/skills/` directory.

### One-Command Install (Recommended)
Run this from the root of your OpenClaw instance (the project that contains `openclaw-config/`):

```bash
rm -rf /tmp/attribuly-skills \
  && git clone --depth 1 https://github.com/Alexchulee/Attribuly.git /tmp/attribuly-skills \
  && mkdir -p ./openclaw-config/skills \
  && rsync -av /tmp/attribuly-skills/openclaw-config/skills/*.md ./openclaw-config/skills/
```

### Install as a Git Submodule (Keeps Updates Easy)
Run this from the root of your OpenClaw instance:

```bash
git submodule add https://github.com/Alexchulee/Attribuly.git vendor/attribuly \
  && mkdir -p ./openclaw-config/skills \
  && rsync -av vendor/attribuly/openclaw-config/skills/*.md ./openclaw-config/skills/
```

To pull future updates:

```bash
git submodule update --remote --merge
rsync -av vendor/attribuly/openclaw-config/skills/*.md ./openclaw-config/skills/
```

## Publish to Clawhub (Optional)

If you’re publishing each skill to Clawhub, use the folder-per-skill layout under:
- `openclaw-config/skills_publish/`

Then run:

```bash
./publish_to_clawhub.sh
```
