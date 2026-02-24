# 项目状态管理 (Project State Management)

对于复杂的项目，简单的看板（Kanban）往往不足以捕捉上下文。此用例使用 **事件溯源 (Event Sourcing)** 模型，其中 OpenClaw 将项目的每一次更新、决策和阻碍记录为不可变的事件流。这允许生成完美的状态报告和历史回溯。

## 概念

与其只保存任务的“当前状态”（例如，“进行中”），我们保存发生的一切：
1. `TaskCreated`: "添加了数据库架构任务"
2. `ContextAdded`: "决定使用 PostgreSQL 因为我们需要 JSONB"
3. `StatusChanged`: "移动到进行中"
4. `BlockerReported`: "等待 AWS 凭证"
5. `BlockerResolved`: "收到凭证"
6. `TaskCompleted`: "架构已部署"

## 它是如何工作的

### 1. 事件日志
一个简单的追加型数据库表或文件。

```sql
CREATE TABLE events (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  event_type TEXT,
  description TEXT,
  context TEXT,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);
```

### 2. 状态投影 (State Projection)
OpenClaw 读取事件流以构建项目的“当前视图”。它可以回答：
- "我们为什么要在 3 天前改变数据库策略？"
- "这个任务被阻塞了多久？"
- "谁做出的决策最多？"

### 3. 自动更新
当你工作时，你只需告诉 OpenClaw 你在做什么：
> "我正在开始做 API 身份验证。我们将使用 JWT。"

OpenClaw 记录：
- Event: `StatusUpdate`
- Context: "Started API Auth, Strategy: JWT"

### 4. 报告生成
在一天结束时，OpenClaw 生成一份丰富的摘要：

> **今天取得的进展**：
> - 开始了 API 身份验证（决定使用 JWT）。
> - 解决了 AWS 凭证阻碍。
> 
> **当前风险**：
> - 前端集成落后于计划。

## 为什么这更好

- **无损历史**：你永远不会丢失做出决策的 *原因*。
- **上下文感知**：AI 在以后的任务中建议代码或解决方案时，可以“读取”整个项目的历史记录。
- **轻松交接**：新团队成员可以通过重播事件流来赶上进度，而不仅仅是查看静态的任务列表。

## 设置

使用 SQLite 或简单的 JSON 文件 (`events.jsonl`) 即可开始。

**OpenClaw 命令示例**：
- `/log "决定使用 Redis 进行缓存"` -> 追加到日志。
- `/status` -> 读取日志，总结当前状态。
- `/summary 7d` -> 读取过去 7 天的日志，生成周报。
