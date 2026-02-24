# 多源科技新闻摘要

自动聚合、评分和分发来自 109+ 来源（RSS、Twitter/X、GitHub 发布和网页搜索）的科技新闻——全部通过自然语言管理。

## 痛点

要在 AI、开源和前沿技术方面保持更新，需要每天检查数十个 RSS 源、Twitter 帐户、GitHub 仓库和新闻网站。手动策划非常耗时，而且大多数现有工具要么缺乏质量过滤，要么需要复杂的配置。

## 功能介绍

一个按计划运行的四层数据流水线：

1. **RSS 源**（46 个来源）— OpenAI, Hacker News, MIT Tech Review 等
2. **Twitter/X KOL**（44 个帐户）— @karpathy, @sama, @VitalikButerin 等
3. **GitHub 发布**（19 个仓库）— vLLM, LangChain, Ollama, Dify 等
4. **网页搜索**（4 个主题搜索）— 通过 Brave Search API

所有文章被合并，通过标题相似性去重，并进行质量评分（优先来源 +3，多源 +5，时效性 +2，参与度 +1）。最终摘要被分发到 Discord、电子邮件或 Telegram。

该框架完全可定制——在 30 秒内添加你自己的 RSS 源、Twitter 句柄、GitHub 仓库或搜索查询。

## 提示

**安装并设置每日摘要：**
```text
从 ClawHub 安装 tech-news-digest。设置每天上午 9 点向 Discord #tech-news 频道发送每日科技摘要。同时发送到我的电子邮件 myemail@example.com。
```

**添加自定义来源：**
```text
将这些添加到我的科技摘要来源：
- RSS: https://my-company-blog.com/feed
- Twitter: @myFavResearcher
- GitHub: my-org/my-framework
```

**按需生成：**
```text
为过去 24 小时生成科技摘要并发送到这里。
```

## 所需技能

- [tech-news-digest](https://clawhub.ai/skills/tech-news-digest) — 通过 `clawhub install tech-news-digest` 安装
- [gog](https://clawhub.ai/skills/gog)（可选）— 用于通过 Gmail 发送电子邮件

## 环境变量（可选）

- `X_BEARER_TOKEN` — Twitter/X API bearer 令牌用于 KOL 监控
- `BRAVE_API_KEY` — Brave Search API 密钥用于网页搜索层
- `GITHUB_TOKEN` — GitHub 令牌用于更高的 API 速率限制

## 相关链接

- [GitHub 仓库](https://github.com/draco-agent/tech-news-digest)
- [ClawHub 页面](https://clawhub.ai/skills/tech-news-digest)
