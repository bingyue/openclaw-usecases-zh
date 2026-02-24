# 第二大脑 (Second Brain)

构建一个利用 OpenClaw 自动捕获、组织和检索你的想法、笔记和阅读列表的系统。这不仅仅是一个笔记应用程序；它是一个主动的知识伙伴，可以帮助你连接各个点。

## 核心功能

1. **全方位捕获 (Omni-Capture)**：通过 Telegram、电子邮件、语音备忘录或浏览器扩展发送任何内容（链接、想法、PDF）。OpenClaw 会摄取所有内容。
2. **自动标记与摘要**：OpenClaw 阅读你的内容，生成摘要，并应用相关标签（例如，#AI, #Startup, #Cooking）。
3. **向量搜索 (RAG)**：所有内容都被嵌入并存储在向量数据库中。你可以用自然语言提问：“我上周读到的关于强化学习的那篇文章是什么？”
4. **每日回顾**：每天早上，OpenClaw 会展示 3 个随机的旧笔记或链接，以帮助你重新发现遗忘的想法。

## 技术栈

- **前端**：Next.js（用于搜索/浏览 UI）
- **后端**：OpenClaw（处理摄取和查询）
- **数据库**：Supabase (pgvector) 或 ChromaDB
- **输入渠道**：Telegram Bot API, Email Hook

## 它是如何工作的

### 1. 摄取管道
当你向你的 Telegram 机器人发送链接时：
1. OpenClaw 抓取网页内容。
2. 使用 LLM 将其总结为 3 个要点。
3. 生成嵌入 (Embedding)。
4. 保存到数据库。

### 2. 检索 (Q&A)
提示：
```text
I want to build a second brain system where I can review all our notes,
conversations, and memories. Please build that out with Next.js.
Include:
- A searchable list of all memories and conversations
- Global search (Cmd+K) across everything
- Ability to filter by date and type
- Clean, minimal UI
```

### 3. 主动连接
当你保存新笔记时，OpenClaw 会运行后台检查：
> "这个新笔记关于‘习惯养成’似乎与你 2023 年关于‘原子习惯’的笔记有关。要把它们链接起来吗？"

## 为什么使用它

- **停止以此为生**：不需要手动整理文件夹。只需转储并搜索。
- **意外发现**：发现你从未意识到的想法之间的联系。
- **节省时间**：即时检索技术文档、代码片段或你保存的文章。

## 示例工作流

1. **阅读**：你在 Hacker News 上看到一篇很棒的文章。
2. **分享**：你将其分享给你的 "OpenClaw Brain" Telegram 聊天。
3. **处理**：OpenClaw 回复：“已保存。摘要：... 关键词：#Tech #Scaling”。
4. **回顾**：一个月后，你问：“我们有哪些关于扩展数据库的文章？”，OpenClaw 会检索它。
