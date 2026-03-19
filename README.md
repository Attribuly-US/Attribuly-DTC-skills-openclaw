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

See [SKILL_REGISTRY.md](SKILL_REGISTRY.md) for triggers and usage mapping.

## 🛠 How to Install Skills into Your OpenClaw Instance

There are two primary ways to install these Attribuly skills into your own OpenClaw environment. Choose the method that best fits your workflow.

### Method 1: Manual Copy (Quickest)
If you just want to grab a few skills and start using them immediately:

1. **Locate your OpenClaw configuration directory**: Find the `openclaw-config/skills/` folder in your OpenClaw project root.
2. **Download the skills**: Download the specific `.md` files you need from this repository (e.g., `google_ads_performance.md`, `budget_optimization.md`).
3. **Copy to OpenClaw**: Place the downloaded `.md` files directly into your `openclaw-config/skills/` directory.
4. **Reload OpenClaw**: Restart your OpenClaw instance or trigger a skill reload to make the new skills available.

### Method 2: Git Submodule (Recommended for Easy Updates)
If you want to keep your skills up-to-date with the latest improvements from this repository, adding it as a Git submodule is the best approach.

1. Navigate to the root of your OpenClaw instance in your terminal.
2. Add this repository as a submodule:
   ```bash
   git submodule add https://github.com/Alexchulee/Attribuly.git vendor/attribuly
   ```
3. Create the skills directory if it doesn't already exist:
   ```bash
   mkdir -p ./openclaw-config/skills
   ```
4. Sync the skills into your active configuration:
   ```bash
   rsync -av vendor/attribuly/*.md ./openclaw-config/skills/
   ```

**How to pull future updates:**
To ensure you always have the latest skill logic, you can easily pull updates and re-sync them:
```bash
git submodule update --remote --merge
rsync -av vendor/attribuly/*.md ./openclaw-config/skills/
```

*(Tip: You can add the rsync command to your build or deployment script to automate updates!)*

### Post-Installation
Once the `.md` files are successfully placed in your `openclaw-config/skills/` directory, check the [SKILL_REGISTRY.md](SKILL_REGISTRY.md) for details on the specific triggers and required contexts to use each skill effectively.
