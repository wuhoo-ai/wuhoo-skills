# RSS 情感分析 Graceful Degradation 模式 (v3.2)

## 设计原则

**核心规则**: 永远不因"数据稀疏"而放弃情感分析管线。即使 DB 中零文章、30 天窗口全空，也要走完代理策略路径 → 返回中性（0 ELO 调整），不提前 `return {}`。

## 修复前的问题 (v3.1-)

```python
# ❌ 错误模式：硬切断
news_items = connector.fetch_football_news(all_teams, days_back=14)
if not news_items:
    print("⚠️ No recent football news found")  # 恐慌消息
    return {}  # 跳过代理策略，完全放弃
```

后果：RSS cron 暂停超 14 天 → `--news` 完全失效；代理策略被跳过；用户误以为系统故障。

## 修复后的模式 (v3.2)

```python
# ✅ 正确模式：分层 fallback + 始终走代理
news_items = connector.fetch_football_news(all_teams, days_back=14)
if not news_items:
    news_items = connector.fetch_football_news(all_teams, days_back=30)

if news_items:
    sentiment_scores = analyzer.analyze_news_batch(news_items)
else:
    sentiment_scores = {}  # 空字典，仍走代理策略

for team in ALL_TEAMS:
    proxy_score = analyzer.get_proxy_sentiment(team, sentiment_scores)
    ...
```

## 三层 Graceful Degradation

| 层级 | 条件 | 行为 | 消息 |
|------|------|------|------|
| 🟢 正常 | 14天窗口有文章 | 直接情感+代理 | "N articles loaded" |
| 🟡 降级 | 14天空但30天有 | 旧数据情感+代理 | "N articles loaded" |
| 🔴 中性 | 30天也空 | 代理策略→全0 | "all teams → neutral" |
| ❌ 跳过 | DB文件不存在 | return {} | "skipped: DB not found" |

## 关键教训

1. **不用 `return {}` 做硬切断** — 代理策略可处理空输入
2. **不恐慌措辞** — "all teams → neutral" 而非 "no news found"
3. **数据是动态的** — 不是"不完整"，而是"当前窗口无数据"
4. **分层 fallback** — 14天→30天比单窗口更鲁棒
