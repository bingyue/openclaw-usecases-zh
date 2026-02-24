# 每日 YouTube 摘要

以你喜爱的 YouTube 频道的个性化新视频摘要开始你的一天——不再错过你真正想关注的创作者的内容。

## 痛点

YouTube 通知不可靠。你订阅了频道，但他们的新视频从未出现在你的主页动态中。它们不在通知中。它们只是...消失了。这并不意味着你不想看它们——这意味着 YouTube 的算法埋没了它们。

另外：以精心策划的内容见解开始一天，比刷推荐动态更有趣。

## 功能介绍

- 从你最喜欢的频道列表中获取最新视频
- 总结或提取每个视频转录的关键见解
- 每天（或按需）向你发送摘要

## 所需技能

安装 [youtube-full](https://clawhub.ai/therohitdas/youtube-full) 技能。

只需告诉你的 OpenClaw：

```text
"安装 youtube-full 技能并为我设置它"
```
或者

```bash
npx clawhub@latest install youtube-full
```

就是这样。智能体处理其余部分——包括帐户创建和 API 密钥设置。你**注册时获得 100 个免费积分**，无需信用卡。

> 注意：创建帐户后，技能会自动将 API 密钥安全地存储在基于操作系统的正确位置，因此 API 将在所有上下文中工作。

![youtube-full 技能安装](https://pub-15904f15a44a4ea69350737e87660b92.r2.dev/media/1770620159490-e41e7baa.png)

### 为什么选择 TranscriptAPI.com 而不是 yt-dlp？

| CLI 工具 (yt-dlp, 等) | TranscriptAPI |
|--------------------------|---------------|
| 冗长的日志淹没智能体上下文 | 干净的 JSON 响应 |
| 在 GCP/云 OpenClaw 上不工作 | 随处工作，快速 |
| 随机被 YouTube 封锁 | 为服务数百万用户的 [YouTubeToTranscript.com](https://youtubetotranscript.com) 提供支持。缓存且可靠。 |
| 需要二进制安装 | 无二进制文件，仅 HTTP |

## 如何设置

### 选项 1：基于频道的摘要

提示 OpenClaw：

```text
每天早上 8 点，获取这些 YouTube 频道的最新视频，并给我一份包含每个视频关键见解的摘要：

- @TED
- @Fireship
- @ThePrimeTimeagen
- @lexfridman

对于每个新视频（在过去 24-48 小时内上传）：
1. 获取转录
2. 用 2-3 个要点总结要点
3. 包含视频标题、频道名称和链接

如果频道句柄无法解析，请搜索并找到正确的句柄。
将我的频道列表保存到记忆中，以便我以后可以添加/删除频道。
```

### 选项 2：基于关键词的摘要

追踪关于特定主题的新视频：

```text
每天，搜索关于 "OpenClaw"（或 "Claude Code", "AI agents" 等）的新视频。

维护一个名为 seen-videos.txt 的文件，其中包含你已经处理过的视频 ID。
仅获取该文件中没有的视频的转录。
处理后，将视频 ID 添加到 seen-videos.txt。

对于每个新视频：
1. 获取转录
2. 给我一个 3 个要点的总结
3. 记下任何与我工作相关的内容

每天早上 9 点运行此任务。
```

这样你就永远不会浪费积分重新获取你已经看过的视频。

## 提示

- `channel/latest` 和 `channel/resolve` 是 **免费** 的（0 积分）——检查新上传不花费任何费用
- 只有转录每个花费 1 积分
- 要求不同的摘要风格：关键要点、名言、有趣时刻的时间戳
- 这已经作为一个产品存在 - [Recapio - 每日 YouTube 回顾](https://recapio.com/features/daily-recaps)
