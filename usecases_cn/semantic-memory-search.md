# 语义记忆搜索 (Semantic Memory Search)

OpenClaw 的内置记忆系统将所有内容存储为 Markdown 文件——但随着记忆在数周和数月内增长，找到上周二做出的那个决定变得不可能。没有搜索功能，只能滚动浏览文件。

此用例在 OpenClaw 现有的 Markdown 记忆文件之上添加了**基于向量的语义搜索**，使用 [memsearch](https://github.com/zilliztech/memsearch)，因此你可以通过含义而不仅仅是关键字立即找到任何过去的记忆。

## 它的作用

- 通过一条命令将你所有的 OpenClaw Markdown 记忆文件索引到向量数据库 (Milvus) 中
- 按含义搜索：“我们选择了什么缓存方案？”即使单词“缓存”没有出现，也能找到相关的记忆
- 混合搜索（密集向量 + BM25 全文）配合 RRF 重排序以获得最佳结果
- SHA-256 内容哈希意味着未更改的文件永远不会被重新嵌入——零浪费的 API 调用
- 文件监视器在记忆文件更改时自动重新索引，因此索引始终是最新的
- 适用于任何嵌入提供商：OpenAI, Google, Voyage, Ollama, 或完全本地（无需 API 密钥）

## 痛点

OpenClaw 的记忆存储为纯 Markdown 文件。这对于可移植性和人类可读性非常棒，但它没有搜索功能。随着你的记忆增长，你要么必须通过 grep 查找文件（仅限关键字，错过语义匹配），要么将整个文件加载到上下文中（在不相关的内容上浪费 token）。你需要一种方法来问“关于 X 我决定了什么？”，并获得确切的相关块，无论措辞如何。

## 你需要的技能

- 不需要 OpenClaw 技能——memsearch 是一个独立的 Python CLI/库
- Python 3.10+ 以及 pip 或 uv

## 如何设置

1. 安装 memsearch:
```bash
pip install memsearch
```

2. 运行交互式配置向导:
```bash
memsearch config init
```

3. 索引你的 OpenClaw 记忆目录:
```bash
memsearch index ~/path/to/your/memory/
```

4. 按含义搜索你的记忆:
```bash
memsearch search "what caching solution did we pick?"
```

5. 对于实时同步，启动文件监视器——它会在每次文件更改时自动索引:
```bash
memsearch watch ~/path/to/your/memory/
```

6. 对于完全本地的设置（无 API 密钥），安装本地嵌入提供商:
```bash
pip install "memsearch[local]"
memsearch config set embedding.provider local
memsearch index ~/path/to/your/memory/
```

## 关键见解

- **Markdown 保持为事实来源。** 向量索引只是一个派生的缓存——你可以随时使用 `memsearch index` 重建它。你的记忆文件永远不会被修改。
- **智能去重省钱。** 每个块都由 SHA-256 内容哈希标识。重新运行 `index` 仅嵌入新的或更改的内容，因此你可以经常运行它而不会浪费嵌入 API 调用。
- **混合搜索优于纯向量搜索。** 通过倒数排名融合 (Reciprocal Rank Fusion) 将语义相似性（密集向量）与关键字匹配 (BM25) 结合起来，可以捕获基于含义和精确匹配的查询。

## 相关链接

- [memsearch GitHub](https://github.com/zilliztech/memsearch) — 支持此用例的库
- [memsearch Documentation](https://zilliztech.github.io/memsearch/) — 完整的 CLI 参考、Python API 和架构
- [OpenClaw](https://github.com/openclaw/openclaw) — 启发 memsearch 的记忆架构
- [Milvus](https://milvus.io/) — 向量数据库后端
