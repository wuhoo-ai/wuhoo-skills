# RSS Sentiment 集成修复记录 (v3.2)

## 修复点: `load_news_sentiment()` 硬切断

**文件**: `wc2026_predict.py`, 函数 `load_news_sentiment(enable_news=False)`

### 问题 (v3.1-)
```python
news_items = connector.fetch_football_news(all_teams_list, days_back=14)
if not news_items:
    print("⚠️ No recent football news found", file=sys.stderr)
    return {}   # ← 硬切断，跳过代理策略
```

当 RSS DB 在 14 天窗口内零文章时，情感分析完全放弃。即使同洲代理策略可以给 48 队生成中性值，也被一刀切掉了。

### 修复 (v3.2)
```python
# 双层窗口 fallback
news_items = connector.fetch_football_news(all_teams_list, days_back=14)
if not news_items:
    news_items = connector.fetch_football_news(all_teams_list, days_back=30)

if news_items:
    sentiment_scores = analyzer.analyze_news_batch(news_items)
else:
    sentiment_scores = {}  # ← 继续走，不切断

# 代理策略始终运行
for team in ALL_TEAMS:
    proxy_score = analyzer.get_proxy_sentiment(team, sentiment_scores)
    # ... → neutral (0) when no data
```

### 验证 (2026-06-06)
- DB: 1,464 篇足球文章，最新 2026-06-06
- 14 天窗口: 327 篇命中
- 覆盖率: 45 direct + 3 proxy = **48/48 (100%)**
- ELO 调整范围: -20 到 +2

### 代理策略工作原理
1. 球队有直接新闻 → 直接用
2. 球队无新闻但有同洲代理 → 同洲强队中位数 × 0.5
3. 完全无数据 → 返回 0（中性），不影响其他模型层

## 已知数据源状态 (2026-06-06)

| 来源 | 状态 | 备注 |
|------|------|------|
| eloratings.net | JS 渲染页面 | 不可直接 curl，search snippet 可用 |
| international-football.net | 429 限速 | fetch_elo.py 被限 |
| RSS news.db | ✅ 活跃 | 1,464 篇，最新今日 |
