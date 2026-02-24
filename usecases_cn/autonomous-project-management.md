# 使用子智能体进行自主项目管理

管理具有多个并行工作流的复杂项目是令人筋疲力尽的。你最终会不断地切换上下文，跨工具追踪状态，并手动协调交接。

本用例实现了一种去中心化的项目管理模式，其中子智能体自主处理任务，通过共享状态文件而不是中央编排器进行协调。

## 痛点

传统的编排器模式会产生瓶颈——主智能体变成了交通指挥。对于复杂的项目（多仓库重构、研究冲刺、内容流水线），你需要能够并行工作而无需持续监督的智能体。

## 功能介绍

- **去中心化协调**：智能体读取/写入共享的 `STATE.yaml` 文件
- **并行执行**：多个子智能体同时处理独立任务
- **无编排器开销**：主会话保持精简（CEO 模式——仅负责战略）
- **自文档化**：所有任务状态都持久化在版本控制的文件中

## 核心模式：STATE.yaml

每个项目维护一个 `STATE.yaml` 文件，作为单一事实来源：

```yaml
# STATE.yaml - 项目协调文件
project: website-redesign
updated: 2026-02-10T14:30:00Z

tasks:
  - id: homepage-hero
    status: in_progress
    owner: pm-frontend
    started: 2026-02-10T12:00:00Z
    notes: "正在处理响应式布局"
    
  - id: api-auth
    status: done
    owner: pm-backend
    completed: 2026-02-10T14:00:00Z
    output: "src/api/auth.ts"
    
  - id: content-migration
    status: blocked
    owner: pm-content
    blocked_by: api-auth
    notes: "等待新的端点模式"

next_actions:
  - "pm-content: 现在 api-auth 已完成，恢复迁移"
  - "pm-frontend: 与设计团队审查 hero 部分"
```

## 工作原理

1. **主智能体接收任务** → 孵化具有特定范围的子智能体
2. **子智能体读取 STATE.yaml** → 找到其分配的任务
3. **子智能体自主工作** → 根据进度更新 STATE.yaml
4. **其他智能体轮询 STATE.yaml** → 拾取未阻塞的工作
5. **主智能体定期检查** → 审查状态，调整优先级

## 所需技能

- `sessions_spawn` / `sessions_send` 用于子智能体管理
- 文件系统访问权限用于 STATE.yaml
- Git 用于状态版本控制（可选但推荐）

## 设置：AGENTS.md 配置

```text
## PM 授权模式

主会话 = 仅协调者。所有执行都交给子智能体。

工作流：
1. 新任务到达
2. 检查 PROJECT_REGISTRY.md 以获取现有的 PM
3. 如果 PM 存在 → sessions_send(label="pm-xxx", message="[任务]")
4. 如果是新项目 → sessions_spawn(label="pm-xxx", task="[任务]")
5. PM 执行，更新 STATE.yaml，汇报
6. 主智能体向用户总结

规则：
1. 主会话：最多 0-2 次工具调用（仅 spawn/send）
2. PM 拥有他们的 STATE.yaml 文件
3. PM 可以为并行子任务孵化子-子智能体
4. 所有状态更改都提交到 git
```

## 示例：孵化一个 PM

```text
用户: "重构 auth 模块并更新文档"

主智能体:
1. 检查注册表 → 没有活跃的 pm-auth
2. 孵化: sessions_spawn(
     label="pm-auth-refactor",
     task="重构 auth 模块，更新文档。在 STATE.yaml 中追踪"
   )
3. 回复: "已孵化 pm-auth-refactor。完成后我会汇报。"

PM 子智能体:
1. 创建带有任务分解的 STATE.yaml
2. 处理任务，更新状态
3. 提交更改
4. 向主智能体汇报完成
```

## 关键见解

- **STATE.yaml > 编排器**：基于文件的协调比消息传递更易扩展
- **Git 作为审计日志**：提交 STATE.yaml 更改以获取完整历史记录
- **标签约定很重要**：使用 `pm-{project}-{scope}` 以便于追踪
- **精简的主会话**：主智能体做得越少，响应越快

## 基于

此模式受 [Nicholas Carlini 的方法](https://nicholas.carlini.com/) 启发，即让智能体自组织而不是微观管理它们。

## 相关链接

- [OpenClaw 子智能体文档](https://github.com/openclaw/openclaw)
- [Anthropic: 构建高效智能体](https://www.anthropic.com/research/building-effective-agents)
