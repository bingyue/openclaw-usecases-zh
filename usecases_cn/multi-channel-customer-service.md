# 多渠道 AI 客户服务平台

小型企业要在多个应用程序中兼顾 WhatsApp、Instagram DM、电子邮件和 Google 评论。客户期望 24/7 即时响应，但雇用全天候员工覆盖成本高昂。

此用例将所有客户接触点整合到一个代表你智能响应的单一 AI 驱动收件箱中。

## 功能介绍

- **统一收件箱**：WhatsApp Business、Instagram DM、Gmail 和 Google 评论集中在一处
- **AI 自动回复**：自动处理常见问题解答、预约请求和常见查询
- **人工切换**：升级复杂问题或标记以供审查
- **测试模式**：向客户演示系统而不影响真实客户
- **业务上下文**：针对你的服务、定价和政策进行培训

## 真实业务示例

在 Futurist Systems，我们为本地服务企业（餐厅、诊所、沙龙）部署了此系统。一家餐厅将响应时间从 4 小时以上缩短至 2 分钟以内，自动处理了 80% 的查询。

## 所需技能

- WhatsApp Business API 集成
- Instagram Graph API（通过 Meta Business）
- `gog` CLI 用于 Gmail
- Google Business Profile API 用于评论
- AGENTS.md 中的消息路由逻辑

## 如何设置

1. **连接渠道** 通过 OpenClaw 配置：
   - WhatsApp Business API（通过 360dialog 或官方 API）
   - Instagram 通过 Meta Business Suite
   - Gmail 通过 `gog` OAuth
   - Google Business Profile API 令牌

2. **创建业务知识库**：
   - 服务和定价
   - 营业时间和位置
   - 常见问题解答
   - 升级触发器（例如，投诉、退款请求）

3. **配置 AGENTS.md** 带有路由逻辑：

```text
## 客户服务模式

收到客户消息时：

1. 识别渠道（WhatsApp/Instagram/电子邮件/评论）
2. 检查是否为此客户启用了测试模式
3. 分类意图：
   - FAQ → 从知识库响应
   - 预约 → 检查可用性，确认预订
   - 投诉 → 标记为人工审查，确认收到
   - 评论 → 感谢反馈，解决疑虑

响应风格：
- 友好、专业、简洁
- 匹配客户的语言（ES/EN/UA）
- 永远不要编造知识库中没有的信息
- 署名为企业名称

测试模式：
- 响应前缀为 [TEST]
- 记录但不发送到真实渠道
```

4. **设置心跳检查** 用于响应监控：

```text
## 心跳：客户服务检查

每 30 分钟：
- 检查超过 5 分钟未回复的消息
- 如果响应队列积压则发出警报
- 记录每日响应指标
```

## 关键见解

- **语言检测很重要**：自动检测并以客户的语言响应
- **测试模式至关重要**：客户需要在上线前看到它工作
- **切换规则**：定义明确的升级触发器以避免 AI 过度干预
- **响应模板**：针对敏感话题（退款、投诉）的预先批准模板

## 相关链接

- [WhatsApp Business API](https://developers.facebook.com/docs/whatsapp)
- [Instagram Messaging API](https://developers.facebook.com/docs/instagram-api/guides/messaging)
- [Google Business Profile API](https://developers.google.com/my-business)
