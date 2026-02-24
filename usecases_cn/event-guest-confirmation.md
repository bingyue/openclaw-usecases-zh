# 活动嘉宾确认

你正在举办一场活动——晚宴、婚礼、公司外出活动——你需要确认嘉宾名单的出席情况。手动给 20 多人打电话很乏味：你玩电话捉迷藏，忘记谁说了什么，并失去饮食限制或携带同伴的记录。发短信有时有效，但人们会忽略消息。真正的电话通话获得的回应率要高得多。

此用例让 OpenClaw 使用 [SuperCall](https://clawhub.ai/xonder/supercall) 插件呼叫你名单上的每位嘉宾，确认他们是否出席，收集任何备注，并为你编译所有内容。

## 功能介绍

- 遍历嘉宾名单（姓名 + 电话号码）并呼叫每位嘉宾
- AI 以你活动协调员的身份，用友好的角色介绍自己
- 与嘉宾确认活动日期、时间和地点
- 询问他们是否出席，并收集任何备注（饮食需求、携带同伴、到达时间等）
- 所有通话完成后，编译摘要：谁确认了，谁拒绝了，谁没接电话，以及任何备注

## 为什么选择 SuperCall

此用例专门使用 [SuperCall](https://clawhub.ai/xonder/supercall) 插件——而不是内置的 `voice_call` 插件。关键区别：SuperCall 是一个完全独立的语音智能体。通话中的 AI 角色 **只能访问你提供的上下文**（角色名称、目标和开场白）。它无法访问你的网关智能体、你的文件、你的其他工具或任何其他东西。

这对嘉宾确认很重要，因为：

- **安全**：通话另一端的人无法通过对话操纵或访问你的智能体。没有提示注入或数据泄露的风险。
- **更好的对话**：因为 AI 专注于单个特定任务（确认出席），它能保持切题，并且比通用语音智能体更自然地处理通话。
- **批量友好**：你正在给不同的人打很多电话。一个每次通话重置的沙盒角色正是你想要的——对话之间没有干扰。

## 所需技能

- [SuperCall](https://clawhub.ai/xonder/supercall) — 通过 `openclaw plugins install @xonder/supercall` 安装
- 一个带有电话号码的 Twilio 帐户（用于拨打外呼电话）
- 一个 OpenAI API 密钥（用于 GPT-4o Realtime 语音 AI）
- ngrok（用于 webhook 隧道——免费层级即可）

请参阅 [SuperCall README](https://github.com/xonder/supercall) 获取完整的配置说明。

## 如何设置

1. 按照 [设置指南](https://github.com/xonder/supercall#configuration) 安装和配置 SuperCall。确保在你的 OpenClaw 配置中启用了 hook。

2. 准备你的嘉宾名单。你可以直接粘贴在聊天中或保存在文件中：

```text
嘉宾名单 — 夏季烧烤, 6月14日星期六, 下午 4 点, 23 Oak Street

- Sarah Johnson: +15551234567
- Mike Chen: +15559876543
- Rachel Torres: +15555551234
- David Kim: +15558887777
```

3. 提示 OpenClaw：

```text
我需要你为我的活动确认出席情况。详情如下：

活动：夏季烧烤
日期：6月14日星期六下午 4 点
地点：23 Oak Street

这是我的嘉宾名单：
<在此处粘贴你的嘉宾名单>

对于每位嘉宾，使用 supercall 呼叫他们。使用角色 "Jamie, [你的名字] 的活动协调员"。每次通话的目标是确认他们是否出席，并记录任何饮食限制、携带同伴或其他评论。

每次通话后，记录结果。一旦所有通话完成，给我一份完整的摘要：
- 谁确认了
- 谁拒绝了
- 谁没接电话
- 每位嘉宾的任何备注或特殊请求
```

4. OpenClaw 将使用 SuperCall 逐一呼叫每位嘉宾，然后编译结果。你可以随时要求状态更新来检查进度。

## 关键见解

- **从小规模测试开始**：先用 2-3 位嘉宾尝试，确保角色和开场白听起来正确。你可以在呼叫完整名单之前调整语气。
- **注意通话时间**：不要把电话安排得太早或太晚。你可以告诉 OpenClaw 只在特定时间段内呼叫。
- **审查转录**：SuperCall 将转录记录到 `~/clawd/supercall-logs`。第一批次后浏览一下，看看对话进行得如何。
- **无应答处理**：如果有人没接电话，OpenClaw 可以记录下来，你可以决定稍后重试还是通过短信跟进。
- **真正的电话通话需要花钱**：每次通话都会消耗 Twilio 分钟数。在你的 Twilio 帐户中设置适当的限制，特别是对于大型嘉宾名单。

## 相关链接

- [ClawHub 上的 SuperCall](https://clawhub.ai/xonder/supercall)
- [GitHub 上的 SuperCall](https://github.com/xonder/supercall)
- [Twilio 控制台](https://console.twilio.com)
- [OpenAI Realtime API](https://platform.openai.com/docs/guides/realtime)
- [ngrok](https://ngrok.com)
