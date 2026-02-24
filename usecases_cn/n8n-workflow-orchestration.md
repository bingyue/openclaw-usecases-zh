# OpenClaw + n8n 工作流编排

让你的 AI 智能体直接管理 API 密钥并调用外部服务是安全事故的配方。每一个新的集成都意味着 `.env.local` 中多了一个凭证，智能体意外泄露或滥用的攻击面又增加了一个。

此用例描述了一种模式，其中 OpenClaw 通过 webhook 将所有外部 API 交互委托给 n8n 工作流——智能体从不接触凭证，每个集成都是可视化可检查且可锁定的。

## 痛点

当 OpenClaw 直接处理所有事情时，你会遇到三个复合问题：

- **没有可见性**：当它埋在 JavaScript 技能文件或 shell 脚本中时，很难检查智能体实际构建了什么
- **凭证蔓延**：每个 API 密钥都存在于智能体的环境中，离暴露只有一个糟糕的提交之遥
- **浪费令牌**：确定性子任务（发送电子邮件、更新电子表格）本可以作为简单工作流运行，却消耗了 LLM 推理令牌

## 功能介绍

- **代理模式**：OpenClaw 编写带有传入 webhook 的 n8n 工作流，然后为所有未来的 API 交互调用这些 webhook
- **凭证隔离**：API 密钥存在于 n8n 的凭证存储中——智能体只知道 webhook URL
- **可视化调试**：每个工作流都可以在 n8n 的拖放 UI 中检查
- **可锁定工作流**：一旦工作流构建并测试完毕，你将其锁定，以便智能体无法修改它与 API 交互的方式
- **保障步骤**：你可以在 n8n 中添加验证、速率限制和批准门，然后再执行任何外部调用

## 工作原理

1. **智能体设计工作流**：告诉 OpenClaw 你需要什么（例如，“创建一个工作流，当新的 GitHub 问题被标记为 `urgent` 时发送 Slack 消息”）
2. **智能体在 n8n 中构建它**：OpenClaw 通过 n8n 的 API 创建工作流，包括传入 webhook 触发器
3. **你添加凭证**：打开 n8n 的 UI，手动添加你的 Slack 令牌 / GitHub 令牌
4. **你锁定工作流**：防止智能体进一步修改
5. **智能体调用 webhook**：从现在起，OpenClaw 使用 JSON 负载调用 `http://n8n:5678/webhook/my-workflow`——它从不看到 API 密钥

```text
┌──────────────┐     webhook call      ┌─────────────────┐     API call     ┌──────────────┐
│   OpenClaw   │ ───────────────────→  │   n8n Workflow   │ ─────────────→  │  External    │
│   (agent)    │   (no credentials)    │  (locked, with   │  (credentials   │  Service     │
│              │                       │   API keys)      │   stay here)    │  (Slack, etc)│
└──────────────┘                       └─────────────────┘                  └──────────────┘
```

## 所需技能

- `n8n` API 访问权限（用于创建/触发工作流）
- `fetch` 或 `curl` 用于 webhook 调用
- Docker（如果使用预配置的堆栈）
- n8n 凭证管理（手动，每个集成一次性设置）

## 如何设置

### 选项 1：预配置的 Docker 堆栈

社区维护的 Docker Compose 设置 ([openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack)) 在共享 Docker 网络上预连接所有内容：

```bash
git clone https://github.com/caprihan/openclaw-n8n-stack.git
cd openclaw-n8n-stack
cp .env.template .env
# 将你的 Anthropic API 密钥添加到 .env
docker-compose up -d
```

这给你提供了：
- 端口 3456 上的 OpenClaw
- 端口 5678 上的 n8n
- 共享 Docker 网络，以便 OpenClaw 可以直接调用 `http://n8n:5678/webhook/...`
- 预构建的工作流模板（多 LLM 事实核查、电子邮件分类、社交监控）

### 选项 2：手动设置

1. 安装 n8n (`npm install n8n -g` 或通过 Docker 运行)
2. 配置 OpenClaw 以了解 n8n 基本 URL
3. 将此添加到你的 AGENTS.md：

```text
## n8n 集成模式

当我需要与外部 API 交互时：

1. 永远不要在我的环境或技能文件中存储 API 密钥
2. 检查此集成是否已存在 n8n 工作流
3. 如果没有，通过带有 webhook 触发器的 n8n API 创建一个
4. 通知用户添加凭证并锁定工作流
5. 对于所有未来的调用，使用带有 JSON 负载的 webhook URL

工作流命名：openclaw-{service}-{action}
示例：openclaw-slack-send-message

Webhook 调用格式：
curl -X POST http://n8n:5678/webhook/{workflow-name} \
  -H "Content-Type: application/json" \
  -d '{"channel": "#general", "message": "Hello from OpenClaw"}'
```

## 关键见解

- **一举三得**：可观察性（可视化 UI）、安全性（凭证隔离）和性能（确定性工作流不消耗令牌）
- **测试后锁定**：“构建 → 测试 → 锁定”循环至关重要——如果不锁定，智能体可能会悄悄修改工作流
- **n8n 拥有 400+ 集成**：大多数你想要连接的外部服务都已经有 n8n 节点，省去了智能体编写自定义 API 调用的麻烦
- **免费审计跟踪**：n8n 记录每次工作流执行的输入/输出数据

## 灵感来源

此模式由 [Simon Høiberg](https://x.com/SimonHoiberg/status/2020843874382487959) 描述，他概述了此方法优于让 OpenClaw 直接处理 API 交互的三个原因：通过 n8n 可视化 UI 的可观察性，通过凭证隔离的安全性，以及通过将确定性子任务作为工作流运行而不是 LLM 调用来提高性能。[openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack) 仓库提供了一个实现此模式的即运行 Docker Compose 设置。

## 相关链接

- [n8n 文档](https://docs.n8n.io/)
- [openclaw-n8n-stack (Docker 设置)](https://github.com/caprihan/openclaw-n8n-stack)
- [n8n Webhook 触发器文档](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)
