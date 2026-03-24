[English](./README.md) | **简体中文** | [日本語](./README.ja.md)

# AllyClaw OpenClaw 技能库 (Attribuly) - 专为 DTC 电商打造

您的 DTC 电商专属 AI 营销助手；由 Attribuly 第一方数据驱动，易于安装，并支持全托管云部署以实现自动化分析。

### 核心能力：
- **聚焦真实 ROI** — 基于 Attribuly 第一方归因概念（真实 ROAS、新客 ROAS、利润、利润率、LTV、MER），减少广告平台的过度归因。
- **完全可控** — 支持本地或云端部署。您的记忆和策略数据始终保留在安全的专属环境中。
- **可扩展的技能** — 内置自动化触发器。自主分析转化漏斗、预算消耗节奏、创意素材表现及数据差异。无平台绑定。

### 核心使用场景：
- **诊断分析:** 自动检测漏斗瓶颈与落地页转化摩擦。
- **业绩追踪:** 生成 30 秒每日消耗扫描报告或深度的每周高管摘要。
- **创意优化:** 基于真实利润评估 Google/Meta 创意素材，并识别素材疲劳。
- **预算调优:** 获取以利润为先的预算重分配和受众调整建议。

---

## 最新动态与更新日志

**[2026-03-22] v1.1.0 版本正式发布！** 

### [v1.1.0] 新增
- **诊断分析技能套件**: 
  - `funnel-analysis`: 全链路客户转化漏斗分析，精确定位各渠道或落地页的流失瓶颈。
  - `landing-page-analysis`: 诊断落地页转化流失，区分流量质量问题与用户体验摩擦。
  - `attribution-discrepancy`: 识别并诊断广告平台指标（Meta/Google）、Attribuly 统一归因和后端商店数据之间的报告差异。
- **创意分析技能**:
  - `google-creative-analysis`: 提取、处理并分析 Google Ads 的创意表现数据，集成了质量得分、PMax 资产数据及标准化的 A/B 测试评估协议。

### [v1.1.0] 变更
- **技能注册表更新**: 更新了 `SKILL_REGISTRY.md`，将 `funnel-analysis`, `landing-page-analysis`, `attribution-discrepancy`, 和 `google-creative-analysis` 的状态从 `🔜 Planned` 修改为 `✅ Ready`。
- **Google Ads Performance**: 增强了深度的根本原因细分逻辑，当检测到 CTR 问题时，可直接映射触发新的 `google-creative-analysis` 技能。
- **Weekly Marketing Performance**: 整合了漏斗健康检查，并添加了在 CVR 下降时调用新 `funnel-analysis` 技能的触发条件。
- **Meta Ads Performance**: 更新了数据获取的 schema，以包含与新归因管道兼容的标准化参数。
- **Google Creative Analysis 整合**: 将独立的创意分析框架（评估标准、DTC 最佳实践、仪表板架构）直接合并到 `google-creative-analysis` 技能中。

### [v1.1.0] 移除
- 从 `attribution-discrepancy` 的根本原因分析逻辑中删除了过时的“跟踪不足 (Under-tracking)”场景。
- 删除了冗余的独立文件 `creative_analysis_framework.md`。

**[首次发布] v1.0.0**
- 首次创建 `SKILL_REGISTRY.md` 以映射用户意图与 Agent 技能。
- 实现核心业绩分析技能 (`weekly_marketing_performance`, `daily_marketing_pulse`, `google_ads_performance`, `meta_ads_performance`)。
- 实现核心优化技能 (`budget_optimization`, `audience_optimization`, `bid_strategy_optimization`)。

---

## 目录

- [可用技能列表](#可用技能列表)
- [安装指南](#安装指南)
- [全托管云部署](#全托管云部署)
- [安装后配置](#安装后配置)

---

## 可用技能列表

### ✅ 可用 (Ready)

- `weekly-marketing-performance` — 跨渠道每周高管摘要
- `daily-marketing-pulse` — 每日异常检测与预算消耗报告 (30秒极速扫描)
- `google-ads-performance` — Google Ads / PMax 业绩诊断
- `meta-ads-performance` — Meta Ads 业绩诊断 (填补 iOS14 数据鸿沟)
- `budget-optimization` — 利润优先的预算重分配建议
- `audience-optimization` — 受众重叠分析与拉新/重定向调优
- `bid-strategy-optimization` — 基于第一方数据的 tCPA/tROAS 目标设定
- `funnel-analysis` — 客户全生命周期漏斗流失诊断
- `landing-page-analysis` — 识别落地页的流量质量与 UX 摩擦
- `attribution-discrepancy` — 量化并诊断广告网络与后端系统间的报告差异
- `google-creative-analysis` — Google Ads 创意质量得分、PMax 资产及标准化评估

### 🔜 计划中 (Coming Soon)

- `tiktok-ads-performance`
- `meta-creative-analysis`
- `creative-fatigue-detector`
- `product-performance`
- `customer-journey-analysis`
- `ltv-analysis`

有关触发条件和使用映射的详细信息，请参阅 [SKILL\_REGISTRY.md](SKILL_REGISTRY.md)。

---

## 安装指南

### 步骤 0：获取您的 Attribuly API 密钥

在安装技能之前，您需要获取一个 Attribuly API 密钥。这些技能高度依赖 Attribuly 独有的指标（如 `new_order_roas` 和真实利润）来实现自动化分析。

- **付费专属功能：** API 密钥仅对付费计划用户开放。您必须升级您的工作空间才能生成密钥。
- **免费试用：** 如果您是新用户，可以开启 [14天免费试用](https://attribuly.com/pricing/) 来体验平台功能。
- **如何配置：** 获取密钥后，必须将其安全地配置到您的 OpenClaw 环境中。**请勿在聊天对话中直接发送 API Key。**
  1. 打开 OpenClaw 的 **Agent 设置 (Agent Settings)**。
  2. 找到 **环境变量 (Environment Variables)** 或 **密钥管理 (Secrets)** 部分。
  3. 添加一个新的环境变量，命名为 `ATTRIBULY_API_KEY`。
  4. 将您的 API 密钥粘贴为该变量的值并保存。Agent 在调用数据时会自动且安全地读取该变量。

---

您可以通过两种主要方式将这些 Attribuly 技能安装到您的 OpenClaw 环境中。请选择最适合您工作流的方法。

### 方法 1：通过对话安装 (快速开始)

将以下提示词复制到您的 OpenClaw 对话框中，Agent 将自动为您安装：

> Install these skills from https\://github.com/Alexchulee/Attribuly-DTC-skills-openclaw\.git

### 方法 2：Git Submodule (推荐，便于更新)

如果您希望始终保持技能库为最新版本，添加 Git 子模块是最佳方案。

1. 在终端中导航到您的 OpenClaw 实例根目录。
2. 将此仓库添加为子模块：
   ```bash
   git submodule add https://github.com/Alexchulee/Attribuly.git vendor/attribuly
   ```
3. 如果 `skills` 目录不存在，请先创建：
   ```bash
   mkdir -p ./openclaw-config/skills
   ```
4. 将技能文件夹同步到您的活动配置目录：
   ```bash
   rsync -av --exclude=".*" --exclude="LICENSE" vendor/attribuly/ ./openclaw-config/skills/attribuly-dtc-analyst/
   ```

**如何拉取后续更新：**
为确保您始终使用最新的技能逻辑，您可以轻松拉取更新并重新同步：

```bash
git submodule update --remote --merge
rsync -av --exclude=".*" --exclude="LICENSE" vendor/attribuly/ ./openclaw-config/skills/attribuly-dtc-analyst/
```

### 步骤 3：初始化 Agent 角色 (Rule & Soul)

为了确保 Agent 表现得像一个专业的 DTC 增长伙伴，您需要配置其核心身份。OpenClaw 会自动将工作区引导文件（bootstrap files）注入到其系统提示词中。

**自动化方法（推荐）：**
直接将角色提示词复制到您的 Agent 工作区并命名为 `SOUL.md`（如果文件已存在则追加）：
```bash
cp vendor/attribuly/role_prompt.md ./openclaw-config/SOUL.md
```
*(如果您使用的是特定的多智能体设置，请将其复制到 `~/.openclaw/agents/<您的agent名称>/agent.md`)*

**手动方法（通过对话框）：**
1. 打开此仓库中的 [`role_prompt.md`](role_prompt.md) 文件。
2. 复制文件的全部内容。
3. 将其粘贴到您的 OpenClaw 聊天/对话框中，以初始化 Agent 的规则、灵魂和角色。

---

## 全托管云部署

如果您不想在本地运行 OpenClaw，而是更倾向于使用 24 小时在线的全托管环境来运行您的 Attribuly 技能和 LLM，我们推荐使用 **ModelScope Cloud Hosting (魔搭社区云托管)** 或 **AWS Bedrock / SageMaker**。

> **重要提示**：全托管云环境的访问权限目前正在分阶段推出。请填写 [加入 AllyClaw 候补名单表单](https://attribuly.sg.larksuite.com/share/base/form/shrlgSK0KaktsDwbTJqPkjDczCd) 以申请优先访问权。您必须是 Attribuly 的付费用户才有资格申请。

---

## 安装后配置

一旦技能包成功放置在您的 `openclaw-config/skills/` 目录中（本地或云端），请查阅 [SKILL\_REGISTRY.md](SKILL_REGISTRY.md) 以获取有关特定触发器和有效使用每个技能所需上下文的详细信息。
