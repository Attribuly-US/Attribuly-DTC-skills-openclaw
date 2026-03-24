# ClawHub 技能仓库重构计划

## 🎯 目标
将当前平铺在根目录下的所有 `.md` 技能文件重构为 ClawHub 官方推荐的“多文件夹”结构（1 个目录 = 1 个技能 = 1 个 `SKILL.md`），并将现有的 Markdown 表格元数据转换为 ClawHub 兼容的 YAML Frontmatter，以便顺利上架 ClawHub 并在未来实现自动化同步。

## 📋 涉及的技能文件
以下文件将被重构：
- `attribution_discrepancy.md`
- `audience_optimization.md`
- `bid_strategy_optimization.md`
- `budget_optimization.md`
- `daily_marketing_pulse.md`
- `funnel_analysis.md`
- `google_ads_performance.md`
- `google_creative_analysis.md`
- `landing_page_analysis.md`
- `meta_ads_performance.md`
- `weekly_marketing_performance.md`

## 🛠️ 实施步骤

### 1. 目录结构重组与文件重命名
- 遍历上述所有技能文件，提取其基础名称并将下划线 (`_`) 转换为中划线 (`-`)，以符合 ClawHub 的 slug 命名规范（例如：`funnel_analysis` 变为 `funnel-analysis`）。
- 为每个技能创建一个独立文件夹。
- 将原 `.md` 文件移动到对应的新文件夹中，并统一重命名为 `SKILL.md`。

### 2. 重构元数据为 YAML Frontmatter
- 依次读取每个新的 `SKILL.md` 文件内容。
- 解析并提取文件头部的 Markdown 表格（Skill Metadata），获取 `name`、`description` 和 `version`。
- 将提取出的元数据转换为 ClawHub 标准的 YAML Frontmatter 并插入文件最顶部：
  ```yaml
  ---
  name: <skill-slug>
  version: <version>
  description: <description>
  ---
  ```
- 从文件正文中删除原有的 Markdown 表格，以避免信息冗余。

### 3. 更新项目文档与配置引用
- **更新 `README.md` 及多语言版本 (`README.zh-CN.md`, `README.ja.md`)**：
  - 更新安装说明中的 `rsync` 路径，由于现在变成了文件夹结构，同步命令需要从 `*.md` 更新为同步文件夹。
  - 更新文档中对具体技能文件的提及（如将 `funnel_analysis.md` 替换为 `funnel-analysis`）。
- **更新 `SKILL_REGISTRY.md`**：
  - 更新 "Skill File Naming Convention" 和 "Skill File Structure Template" 的说明，反映最新的目录化规范和 YAML Frontmatter 格式。
  - 更新其中引用的示例文件路径。

### 4. 建立 GitHub Actions 自动化发布流程（可选扩展）
- 在 `.github/workflows/clawhub-sync.yml` 创建自动化工作流配置。
- 配置在 `main` 分支发生变更时，自动遍历所有的技能文件夹并执行 `clawhub publish`，以保持 ClawHub 上的版本与 GitHub 仓库一致。

---
请审核此计划。如果确认无误，我将立即开始执行所有重构步骤！