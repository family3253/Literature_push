# Literature (wenxian skill)

A production-oriented OpenClaw skill for literature retrieval and scheduled delivery (daily/weekly/monthly), with multi-channel push and structured reporting.

---

## 中文说明（详细版）

### 1. Skill 是做什么的

`Literature (wenxian)` 用于把“文献检索 → 筛选评估 → 结构化报告 → 定时推送”固化为稳定流程，适合固定主题的长期追踪。

它不是一次性 prompt，而是可持续运行的自动化任务能力：
- 日报：每天增量检索 + 输出
- 周报：聚合近 7 天日报自动总结
- 月报：聚合近 30 天日报自动总结

> 设计原则：周报/月报优先从日报归档总结，不默认重跑全量检索。

---

### 2. 核心能力

- **多周期调度**
  - 日报默认：每天 08:00（Asia/Shanghai）
  - 周报默认：每周最后一天 08:00（周日）
  - 月报默认：每月最后一天 08:00（带月末二次校验）
- **多渠道推送**
  - Feishu / WeCom / QQBot / Telegram
- **结构化输出**
  - TopN 文献、摘要、10问要点、四维评分、证据等级、分区字段
- **分区字段增强**
  - EasyScholar API + 本地 JCR/中科院分区表
- **可靠性机制**
  - 长文分段发送
  - 失败降级（全文分段 → 短摘要+链接 → 告警）
- **自动联动规则**
  - 首次创建日报时，可自动补建同主题周报/月报（可关闭）

---

### 3. 默认业务规则（可改）

- 默认 TopN：
  - 日报 Top3
  - 周报 Top10
  - 月报 Top20
- 周报/月报每篇应附 1 行入选理由（如证据等级高/分区高/临床价值高）
- 覆盖率门槛：
  - 周报默认至少 4 天日报样本
  - 月报默认至少 18 天日报样本
  - 不足则标注“样本不足，结论仅供参考”

---

### 4. 目录结构

```text
Literature/
├─ SKILL.md                             # 技能主说明与执行规则
├─ README.md                            # 本文档（中英文）
├─ .gitignore
├─ scripts/
│  └─ sync_share_repo_jcr.sh            # 同步上游 JCR 数据脚本
└─ references/
   ├─ JCR2024.csv
   ├─ FQBJCR2025.csv
   ├─ JCR2024_FQBJCR2025_merged.csv
   └─ upstream/share_repo_JCR_manifest.txt
```

---

### 5. 你需要准备的配置

> 所有密钥必须本地配置，不要提交到 GitHub。

#### 5.1 OpenClaw 渠道配置（`openclaw.json`）
按你要推送的渠道启用对应字段：
- `channels.feishu`（appId/appSecret）
- `channels.wecom`（botId/secret）
- `channels.qqbot`（appId/clientSecret/token）
- `channels.telegram`（botToken）

#### 5.2 EasyScholar（可选）
若你需要增强分区/指标字段：
- 配置 `EASYSCHOLAR_SECRET_KEY`
- 推荐使用环境变量或 OpenClaw secrets
- 禁止明文写入仓库文件

#### 5.3 网络能力
- 运行环境需可访问 PubMed
- 若有网络限制，配置代理

#### 5.4 时区
- 默认 `Asia/Shanghai`
- 跨地区部署请统一时区策略，避免 cron 时间偏差

---

### 6. 运行逻辑（简版流程）

#### 日报
1. 检索：PubMed 增量检索（24h）
2. 筛选：去重 + 质量排序
3. 生成：Top3 + 结构化内容
4. 推送：按渠道发送
5. 归档：记录结构化字段，供周/月报复用

#### 周报
1. 读取近 7 天日报归档
2. 统计与聚合（主题、证据等级、趋势）
3. 生成 Top10 与总结
4. 推送

#### 月报
1. 读取近 30 天日报归档
2. 聚合分析 + 月度趋势
3. 生成 Top20 与总结
4. 推送（仅月末有效触发）

---

### 7. 安装与使用

#### 本地安装
将 `Literature/` 放到 OpenClaw skills 目录并重载。

#### 示例指令
- “每天 8 点推送 MDRGNB 日报到企业微信”
- “开启 HCC 周报，周末 8 点推送，Top10”
- “月报改到每月最后一天 9:30，Top20 不变”
- “只开月报，不开日报”

---

### 8. 安全与发布建议

- 不提交任何真实 token/secret/private key
- 发布前执行：
  - `git status`
  - `git diff --cached`
  - 全文搜索敏感词（token/secret/key/password）
- 建议用私有配置文件或 secrets 管理系统承载凭据

---

## English (Detailed)

### 1. What this skill does

`Literature (wenxian)` turns literature operations into a stable workflow:
**retrieve → rank/review → structured report → scheduled delivery**.

It is designed for recurring topics, not one-off prompts.

- Daily: incremental retrieval + reporting
- Weekly: summarize from last 7 daily archives
- Monthly: summarize from last 30 daily archives

> Principle: weekly/monthly should be derived from daily archives first, not from full re-retrieval by default.

---

### 2. Core capabilities

- **Multi-cycle scheduling**
  - Daily default: 08:00 (Asia/Shanghai)
  - Weekly default: 08:00 on week end (Sunday)
  - Monthly default: 08:00 on month end (with end-of-month guard)
- **Multi-channel delivery**
  - Feishu / WeCom / QQBot / Telegram
- **Structured outputs**
  - TopN papers, summaries, Q&A points, 4-dimension scoring, evidence levels
- **Journal/ranking enrichment**
  - EasyScholar API + local JCR/CAS datasets
- **Reliability fallback**
  - segmentation for long messages
  - fallback chain when delivery fails
- **Auto-linking behavior**
  - daily creation can auto-create weekly/monthly jobs for the same topic

---

### 3. Default business rules (customizable)

- Default TopN:
  - Daily Top3
  - Weekly Top10
  - Monthly Top20
- Weekly/Monthly entries should include one-line selection rationale
- Coverage thresholds:
  - Weekly: at least 4 daily samples
  - Monthly: at least 18 daily samples
  - If not met: mark as “insufficient sample coverage”

---

### 4. Project structure

```text
Literature/
├─ SKILL.md
├─ README.md
├─ .gitignore
├─ scripts/
│  └─ sync_share_repo_jcr.sh
└─ references/
   ├─ JCR2024.csv
   ├─ FQBJCR2025.csv
   ├─ JCR2024_FQBJCR2025_merged.csv
   └─ upstream/share_repo_JCR_manifest.txt
```

---

### 5. Required configuration

> Configure all secrets locally. Never commit them.

#### 5.1 OpenClaw channel config (`openclaw.json`)
- `channels.feishu` (appId/appSecret)
- `channels.wecom` (botId/secret)
- `channels.qqbot` (appId/clientSecret/token)
- `channels.telegram` (botToken)

#### 5.2 EasyScholar (optional)
- `EASYSCHOLAR_SECRET_KEY`
- Prefer environment variables or OpenClaw secrets
- Never store plaintext keys in repo files

#### 5.3 Network access
- PubMed must be reachable from runtime environment
- Configure proxy if needed

#### 5.4 Timezone
- Default: `Asia/Shanghai`
- Keep timezone policy explicit in multi-region deployments

---

### 6. Runtime flow (summary)

#### Daily
1. Retrieve (24h incremental)
2. De-duplicate + rank
3. Build Top3 structured report
4. Deliver to target channels
5. Archive structured records for weekly/monthly reuse

#### Weekly
1. Load last 7 days of daily archives
2. Aggregate + trend analysis
3. Build Top10 report
4. Deliver

#### Monthly
1. Load last 30 days of daily archives
2. Aggregate + monthly trend analysis
3. Build Top20 report
4. Deliver (validated month-end trigger)

---

### 7. Installation and usage

#### Local install
Place `Literature/` under your OpenClaw skills directory and reload.

#### Example prompts
- “Push MDRGNB daily report to WeCom at 08:00 every day.”
- “Enable weekly HCC report at week-end 08:00 with Top10.”
- “Move monthly report to 09:30 on month-end, keep Top20.”
- “Enable monthly only, without daily.”

---

### 8. Security and release checklist

- Never commit real token/secret/private key values
- Before push:
  - `git status`
  - `git diff --cached`
  - scan repository for sensitive patterns (token/secret/key/password)
- Use a secrets management approach for production credentials
