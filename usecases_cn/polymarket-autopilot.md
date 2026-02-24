# Polymarket 自动交易 (Polymarket Autopilot)

利用 OpenClaw 自动化你在 Polymarket（一个加密货币预测市场）上的交易策略。此用例演示了如何根据新闻、民意调查数据或特定市场条件以编程方式执行交易。

> **免责声明**：此用例仅用于教育目的。涉及真金白银的交易存在风险。请务必先进行纸面交易（模拟交易）。

## 它是如何工作的

1. **市场扫描器**：OpenClaw 定期扫描 Polymarket API 以寻找符合你标准的市场（例如，高交易量、特定类别、价差差异）。
2. **数据丰富**：对于每个潜在市场，OpenClaw 搜索相关的新闻、民意调查或社交媒体情绪。
3. **决策引擎**：LLM 分析市场数据和外部背景以决定是否交易。
4. **执行**：OpenClaw 通过其 API 在 Polymarket 上执行买入或卖出订单。
5. **投资组合管理**：跟踪头寸并在达到利润目标或止损限制时平仓。

## 关键组件

### 1. 市场观察者 (Cron Job)
每 10 分钟运行一次以获取热门市场。

```yaml
schedule: "*/10 * * * *"
command: "python scripts/fetch_markets.py"
```

### 2. 分析技能
一种获取市场问题并在网上搜索答案/概率的技能。

```python
def analyze_market(question):
    # 搜索 Perplexity 或 Google 以获取相关新闻
    context = search_tool.search(question)
    
    # 询问 LLM 真实概率
    prompt = f"""
    Market Question: {question}
    Context: {context}
    
    Based on the evidence, what is the probability of 'Yes'?
    Return a number between 0 and 1.
    """
    return llm.predict(prompt)
```

### 3. 交易执行
如果估计概率与市场价格显著不同（例如，你的模型说 70%，市场价格为 40  cents），则进行交易。

### 4. 纸面交易数据库 (用于测试)
在投入真实资金之前，将交易记录到数据库中以跟踪性能。

```sql
CREATE TABLE paper_trades (
  id SERIAL PRIMARY KEY,
  market_id TEXT,
  market_name TEXT,
  strategy TEXT,
  direction TEXT,
  entry_price DECIMAL,
  exit_price DECIMAL,
  quantity DECIMAL,
  pnl DECIMAL,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);
```

## 示例策略

- **选举套利**：将聚合民意调查数据（来自 538 或 Nate Silver）与 Polymarket 赔率进行比较。
- **突发新闻**：监控路透社/彭博社的新闻提要。如果发生重大事件（例如，“CEO 辞职”），立即在相关市场进行交易。
- **相关性交易**：如果比特币上涨 >5%，则买入“比特币本周将触及 X”的 Yes 份额。

## 为什么使用 OpenClaw

- **24/7 运行**：市场从不睡觉，你的机器人也不应该睡觉。
- **无情绪**：基于数据和预定义规则进行交易，而不是基于炒作或恐惧。
- **快速执行**：可以比人类更快地分析新闻并做出反应。
