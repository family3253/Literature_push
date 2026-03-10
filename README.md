# Literature (wenxian skill)

## 中文说明

用于 OpenClaw 的文献检索与日报/周报/月报推送 Skill。

### 功能
- 日报：默认每天 08:00（Asia/Shanghai）
- 周报：默认每周最后一天 08:00（周日）
- 月报：默认每月最后一天 08:00（带月末二次校验）
- 周报/月报优先基于日报归档自动总结
- 默认 TopN：日报 3、周报 10、月报 20（可自定义）
- 支持多渠道推送：Feishu / WeCom / QQBot / Telegram

### 发布前已做的隐私处理
- 移除了仓库中的 EasyScholar `secretKey` 明文
- 仓库中不应包含任何 token / secret / cookie / 私钥

### 你需要配置的内容

> 这些都应在本地环境配置，不要提交到 GitHub。

1. OpenClaw 渠道配置（`openclaw.json`）
- `channels.feishu`（appId/appSecret）
- `channels.wecom`（botId/secret）
- `channels.qqbot`（appId/clientSecret/token）
- `channels.telegram`（botToken）

2. EasyScholar key（可选）
- `EASYSCHOLAR_SECRET_KEY`
- 建议使用环境变量或 OpenClaw secrets，不要明文写入仓库

3. PubMed 访问能力
- 确保运行环境可访问 PubMed
- 如有代理，配置对应网络代理

4. 时区
- 默认 `Asia/Shanghai`（可按需修改）

### 安装（本地）
将本目录作为 skill 放到 OpenClaw skills 目录下，重载后可触发使用。

### 使用示例
- “每天 8 点推送 MDRGNB 日报到企业微信”
- “基于日报自动生成周报，每周最后一天早 8 点推送”
- “月报改成每月最后一天 9:30，Top20 保持不变”

### 安全建议
- 不要把 `openclaw.json`（含密钥）提交到仓库
- 提交前运行：
  - `git status`
  - `git diff --cached`
  - 检查是否出现 token/secret/key 字样

---

## English

An OpenClaw skill for literature retrieval and scheduled daily/weekly/monthly delivery.

### Features
- Daily report: default at 08:00 (Asia/Shanghai)
- Weekly report: default at 08:00 on the last day of each week (Sunday)
- Monthly report: default at 08:00 on the last day of each month (with end-of-month guard)
- Weekly/Monthly reports are generated primarily by summarizing archived daily reports
- Default TopN: Daily 3, Weekly 10, Monthly 20 (customizable)
- Multi-channel delivery: Feishu / WeCom / QQBot / Telegram

### Privacy sanitization done before publishing
- Removed plaintext EasyScholar `secretKey`
- No token / secret / cookie / private key should be committed

### Required configuration

> Configure these locally. Do NOT commit secrets to GitHub.

1. OpenClaw channel config (`openclaw.json`)
- `channels.feishu` (appId/appSecret)
- `channels.wecom` (botId/secret)
- `channels.qqbot` (appId/clientSecret/token)
- `channels.telegram` (botToken)

2. EasyScholar key (optional)
- `EASYSCHOLAR_SECRET_KEY`
- Recommended: use environment variables or OpenClaw secrets

3. PubMed connectivity
- Ensure PubMed is reachable from your runtime environment
- Configure proxy if needed

4. Timezone
- Default: `Asia/Shanghai` (change if needed)

### Local install
Place this folder as a skill under your OpenClaw skills directory, then reload OpenClaw.

### Example prompts
- "Push MDRGNB daily report to WeCom at 8:00 every day"
- "Generate weekly report from daily archives and push at 8:00 on week end"
- "Change monthly report to 09:30 on month end, keep Top20"

### Security checklist
- Never commit `openclaw.json` with real secrets
- Before each push, run:
  - `git status`
  - `git diff --cached`
  - scan for token/secret/key patterns
