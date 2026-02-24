# YouTube 内容流水线 (YouTube Content Pipeline)

作为一名每日更新的 YouTube 创作者，在网络和 X/Twitter 上寻找新鲜、及时的视频创意非常耗时。跟踪你已经涵盖的内容可以防止重复，并帮助你保持领先趋势。

此工作流自动化了整个内容搜寻和研究流水线：

• 每小时运行一次 Cron 作业，扫描突发 AI 新闻（网络 + X/Twitter）并向 Telegram 推送视频创意
• 维护一个包含观看次数和主题分析的 90 天视频目录，以避免重新覆盖主题
• 将所有推介存储在 SQLite 数据库中，并带有用于语义去重的向量嵌入（这样你就永远不会收到两次相同的想法推介）
• 当你在 Slack 中分享链接时，OpenClaw 会研究该主题，在 X 上搜索相关帖子，查询你的知识库，并创建一个包含完整大纲的 Asana 卡片

## 你需要的技能

- `web_search` (内置)
- [x-research-v2](https://clawhub.ai) 或自定义 X/Twitter 搜索技能
- [knowledge-base](https://clawhub.ai) 技能用于 RAG
- Asana 集成 (或 Todoist)
- `gog` CLI 用于 YouTube Analytics
- 用于接收推介的 Telegram 主题 (Topic)

## 如何设置

1. 设置一个 Telegram 主题用于视频创意并在 OpenClaw 中配置它。
2. 安装 knowledge-base 技能和 x-research 技能。
3. 创建一个用于推介跟踪的 SQLite 数据库：
```sql
CREATE TABLE pitches (
  id INTEGER PRIMARY KEY,
  timestamp TEXT,
  topic TEXT,
  embedding BLOB,
  sources TEXT
);
```
4. 提示 OpenClaw:
```text
Run an hourly cron job to:
1. Search web and X/Twitter for breaking AI news
2. Check against my 90-day YouTube catalog (fetch from YouTube Analytics via gog)
3. Check semantic similarity against all past pitches in the database
4. If novel, pitch the idea to my Telegram "video ideas" topic with sources

Also: when I share a link in Slack #ai_trends, automatically:
1. Research the topic
2. Search X for related posts
3. Query my knowledge base
4. Create an Asana card in Video Pipeline with a full outline
```
