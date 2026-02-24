# 多渠道个人助理

在应用程序之间切换上下文以管理任务、安排事件、发送消息和追踪工作是令人筋疲力尽的。你想要一个路由到所有工具的单一界面。

此工作流将所有内容整合到一个单一的 AI 助理中：

• Telegram 作为主要界面，具有基于主题的路由（用于视频创意、CRM、财报、配置等的不同主题）
• Slack 集成用于团队协作（任务分配、知识库保存、视频创意触发）
• Google Workspace：创建日历事件、管理电子邮件、上传到 Drive——全部通过聊天完成
• Todoist 用于快速任务捕获
• Asana 用于项目管理
• 自动提醒：垃圾日、每周公司信件等

## 所需技能

- `gog` CLI (Google Workspace)
- Slack 集成（机器人 + 用户令牌）
- Todoist API 或技能
- Asana API 或技能
- 配置了多个主题的 Telegram 频道

## 如何设置

1. 为不同的上下文设置 Telegram 主题：
   - `config` — 机器人设置和调试
   - `updates` — 状态和通知
   - `video-ideas` — 内容流水线和研究
   - `personal-crm` — 联系人管理和会议准备
   - `earnings` — 财务追踪
   - `knowledge-base` — RAG 摄取和查询

2. 通过 OpenClaw 配置连接所有工具：
   - Google OAuth (Gmail, Calendar, Drive)
   - Slack（应用程序 + 用户令牌）
   - Todoist API 令牌
   - Asana API 令牌

3. 提示 OpenClaw：
```text
你是我的多渠道助理。根据上下文路由请求：

Telegram 主题：
- "config" → 系统设置、调试
- "updates" → 每日摘要、提醒、日历
- "video-ideas" → 内容流水线和研究
- "personal-crm" → 联系人查询和会议准备
- "earnings" → 财务追踪
- "knowledge-base" → 保存和搜索内容

当我要求你：
- "Add [任务] to my todo" → 使用 Todoist
- "Create a card for [主题]" → 使用 Asana 视频流水线项目
- "Schedule [事件]" → 使用 gog calendar
- "Email [人] about [主题]" → 通过 gog gmail 起草电子邮件
- "Upload [文件] to Drive" → 使用 gog drive

设置自动提醒：
- 周一晚上 6 点："🗑️ 明天是垃圾日"
- 周五下午 3 点："✍️ 该写每周公司更新了"
```

4. 单独测试每个集成，然后测试跨工作流交互（例如，将 Slack 链接保存到知识库，然后在视频研究卡片中使用它）。
