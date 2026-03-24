# Clawhub 安全告警分析与修复计划

## 一、 安全告警分析

根据 Security Scan 报告，当前 OpenClaw 技能存在以下几个高危安全问题：

1. **凭证声明缺失 (Missing Credential Declarations)**
   - **问题表现**：技能的运行明确需要调用 Attribuly 的 API（需要 `ApiKey`）以及可能的 Google Ads API（需要 OAuth tokens）。但是在技能的注册元数据（如 `SKILL.md` 的 Frontmatter）中，`requires.env` 或其他凭证字段为空。
   - **安全风险**：平台无法通过标准的安全凭证管理器 (Secret Manager) 来注入密钥，导致凭证管理不规范。

2. **高风险的凭证收集与存储 (Unsafe Credential Handling & Storage)**
   - **问题表现**：在 `role_prompt.md` 中，指令明确要求 Agent 在 onboarding 阶段收集客户的 API Key，并将其保存到“长期记忆 (long-term memory)”中。
   - **安全风险**：长期记忆通常不具备针对机密信息设计的加密和访问控制。要求 Agent 收集并在记忆中持久化存储明文的 API Key 是一种过度授权（Over-privilege）行为，严重增加了凭证泄露的爆炸半径（Blast Radius）。

3. **缺乏安全处理指引 (Lack of Safe-handling Controls)**
   - **问题表现**：技能文档没有向用户或 Agent 提供如何安全读取和使用这些敏感 API Key 的规范指引。

## 二、 修复方案步骤 (Action Plan)

为了解决上述问题，我们需要对代码库中的核心配置文件进行以下修改：

### 步骤 1：更新 `SKILL.md` 的元数据 (Metadata)
- **目标文件**：`SKILL.md`
- **操作**：在 YAML frontmatter 中添加 `requires.env` 声明，明确指出该技能需要平台注入哪些环境变量凭证（如 `ATTRIBULY_API_KEY`）。
- **目的**：符合 Clawhub 的规范，让平台在安装时通过 Secret Store 提示用户安全地配置这些凭证。

### 步骤 2：修改 `role_prompt.md`，移除危险的凭证收集指令
- **目标文件**：`role_prompt.md`
- **操作 1**：在 **Step 3: Current State** 中，移除向用户询问 API 是否已提供（或收集 API Key）的相关话术。
- **操作 2**：在 **Step 4: Save to Memory** 中，彻底删除“将 API Key 保存到长期记忆”的指令。
- **操作 3**：在文档中补充明确的 **SECURITY WARNING（安全警告）**，严格禁止 Agent 在对话中索要 API Key 或将其写入记忆，并指示 Agent 只能从环境变量中安全地读取凭证。
- **操作 4**：在 **Attribuly API Reference** 部分，将 Authentication 的说明从单纯的 `ApiKey header` 更新为 `ApiKey header (Read from Environment Variables / Secret Manager, NEVER ask user)`。

### 步骤 3：更新 `SKILL_REGISTRY.md` 中的 API 示例
- **目标文件**：`SKILL_REGISTRY.md`
- **操作**：检查文件中的 API 调用说明（如 Default API Parameters），添加安全注释，强调 `ApiKey` 必须通过平台安全变量获取，而不是写死或从记忆中读取。

### 步骤 4：补充用户配置指南 (解决用户如何在 OpenClaw 中配置 API Key 的问题)
- **目标文件**：`README.md` (以及相关的本地化版本 `README.zh-CN.md` 等) 和 `SKILL.md` 的正文。
- **操作**：添加一个明确的 **"Configuration / 安装与配置"** 章节，指导用户如何在 OpenClaw 中填入 API。
- **内容示例**：
  > **如何配置 API Key：**
  > 1. 请勿在与 Agent 的对话中直接发送您的 API Key。
  > 2. 在 OpenClaw 中安装此技能后，请进入您的 **Agent Settings (Agent 设置) -> Environment Variables / Secrets (环境变量 / 密钥管理)**。
  > 3. 添加一个名为 `ATTRIBULY_API_KEY` 的环境变量，并将其值设置为您在 Attribuly 后台获取的 API Key。
  > 4. Agent 将会在调用数据时自动且安全地读取该环境变量。

---

**等待您的确认**：如果您同意此修复计划，请回复确认，我将立即开始执行上述修改。