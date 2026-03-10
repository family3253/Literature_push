# Literature (wenxian skill)

用于 OpenClaw 的文献检索与日报/周报/月报推送 Skill。

## 功能
- 日报：默认每天 08:00（Asia/Shanghai）
- 周报：默认每周最后一天 08:00（周日）
- 月报：默认每月最后一天 08:00（带月末二次校验）
- 周报/月报优先基于日报归档自动总结
- 默认 TopN：日报 3、周报 10、月报 20（可自定义）
- 支持多渠道推送：Feishu / WeCom / QQBot / Telegram

## 发布前已做的隐私处理
- 移除了仓库中的 EasyScholar `secretKey` 明文
- 仓库中不应包含任何 token / secret / cookie / 私钥

## 你需要配置的内容

> 这些都应在本地环境配置，不要提交到 GitHub。

### 1) OpenClaw 渠道配置
在你的 `openclaw.json` 中配置（按你实际用到的渠道）：
- `channels.feishu`（appId/appSecret）
- `channels.wecom`（botId/secret）
- `channels.qqbot`（appId/clientSecret/token）
- `channels.telegram`（botToken）

### 2) EasyScholar key（可选）
如果需要补充分区字段：
- `EASYSCHOLAR_SECRET_KEY`

建议做法：
- 使用环境变量或 OpenClaw secrets
- 在 SKILL prompt 中引用占位符，不写明文

### 3) PubMed 访问能力
- 确保运行环境可访问 PubMed
- 如有代理，配置对应网络代理

### 4) 时区
- 统一使用 `Asia/Shanghai`（可改）

## 安装（本地）
将本目录作为 skill 放到 OpenClaw skills 目录下，重载后可触发使用。

## 使用示例
- “每天 8 点推送 MDRGNB 日报到企业微信”
- “基于日报自动生成周报，每周最后一天早 8 点推送”
- “月报改成每月最后一天 9:30，Top20 保持不变”

## 安全建议
- 不要把 `openclaw.json`（含密钥）提交到仓库
- 提交前运行：
  - `git status`
  - `git diff --cached`
  - 检查是否出现 token/secret/key 字样
