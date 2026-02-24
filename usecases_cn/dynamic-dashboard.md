# 带有子智能体孵化的动态仪表板

静态仪表板显示陈旧数据，并且需要持续的手动更新。你需要跨多个数据源的实时可见性，而无需构建自定义前端或触及 API 速率限制。

此工作流创建了一个实时仪表板，孵化子智能体以并行获取和处理数据：

• 同时监控多个数据源（API、数据库、GitHub、社交媒体）
• 为每个数据源孵化子智能体以避免阻塞并分担 API 负载
• 将结果聚合到统一的仪表板（文本、HTML 或 Canvas）
• 每 N 分钟用新鲜数据更新
• 当指标跨越阈值时发送警报
• 在数据库中维护历史趋势以进行可视化

## 痛点

构建自定义仪表板需要数周时间。等到完成时，需求已经改变。按顺序轮询多个 API 速度慢且会触及速率限制。你需要现在的洞察力，而不是在编码一个周末之后。

## 功能介绍

你以对话方式定义要监控的内容：“追踪 GitHub stars、Twitter 提及、Polymarket 交易量和系统健康状况。”OpenClaw 孵化子智能体并行获取每个数据源，聚合结果，并将格式化的仪表板传递给 Discord 或作为 HTML 文件。更新按 cron 计划自动运行。

示例仪表板部分：
- **GitHub**: stars, forks, open issues, recent commits
- **社交媒体**: Twitter 提及, Reddit 讨论, Discord 活动
- **市场**: Polymarket 交易量, 预测趋势
- **系统健康**: CPU, 内存, 磁盘使用率, 服务状态

## 所需技能

- 用于并行执行的子智能体孵化
- `github` (gh CLI) 用于 GitHub 指标
- `bird` (Twitter) 用于社交数据
- `web_search` 或 `web_fetch` 用于外部 API
- `postgres` 用于存储历史指标
- Discord 或 Canvas 用于渲染仪表板
- Cron 作业用于计划更新

## 如何设置

1. 设置指标数据库：
```sql
CREATE TABLE metrics (
  id SERIAL PRIMARY KEY,
  source TEXT, -- e.g., "github", "twitter", "polymarket"
  metric_name TEXT,
  metric_value NUMERIC,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE alerts (
  id SERIAL PRIMARY KEY,
  source TEXT,
  condition TEXT,
  threshold NUMERIC,
  last_triggered TIMESTAMPTZ
);
```

2. 创建一个 Discord 频道用于仪表板更新（例如，#dashboard）。

3. 提示 OpenClaw：
```text
你是我的动态仪表板管理器。每 15 分钟，运行一个 cron 作业以：

1. 并行孵化子智能体以从以下来源获取数据：
   - GitHub: stars, forks, open issues, commits (过去 24 小时)
   - Twitter: "@username" 的提及, 情感分析
   - Polymarket: 追踪市场的交易量
   - 系统: CPU, 内存, 磁盘使用率（通过 shell 命令）

2. 每个子智能体将结果写入指标数据库。

3. 聚合所有结果并格式化仪表板：

📊 **仪表板更新** — [时间戳]

**GitHub**
- ⭐ Stars: [数量] (+[变化])
- 🍴 Forks: [数量]
- 🐛 Open Issues: [数量]
- 💻 Commits (24h): [数量]

**社交媒体**
- 🐦 Twitter 提及: [数量]
- 📈 情感: [正面/负面/中性]

**市场**
- 📊 Polymarket 交易量: $[金额]
- 🔥 趋势: [市场名称]

**系统健康**
- 💻 CPU: [使用率]%
- 🧠 内存: [使用率]%
- 💾 磁盘: [使用率]%

4. 发布到 Discord #dashboard。

5. 检查警报条件：
   - 如果 GitHub stars 在 1 小时内变化 > 50 → ping 我
   - 如果系统 CPU > 90% → 警报
   - 如果 Twitter 上出现负面情感激增 → 通知

将所有指标存储在数据库中以进行历史分析。
```

4. 可选：使用 Canvas 渲染带有图表和图形的 HTML 仪表板。

5. 查询历史数据：“向我展示过去 30 天的 GitHub star 增长。”

## 相关链接

- [使用子智能体进行并行处理](https://docs.openclaw.ai/subagents)
- [仪表板设计原则](https://www.nngroup.com/articles/dashboard-design/)
