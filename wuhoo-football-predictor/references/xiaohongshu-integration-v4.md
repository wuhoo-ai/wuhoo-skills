# 小红书集成 — 架构、陷阱与优化方向

## 架构

```
Brave Search API → xiaohongshu_collector.py → xhs_sentiment_cache.json
                                                    ↓
                                              XHSConnector
                                                    ↓
wc2026_predict.py ← sentiment_analyzer.py ← RSS (英文) + XHS (中文)
       ↓                                        ↓
  load_news_sentiment()                   RSS 50% + XHS 50% 融合
```

## Brave Search 域名过滤陷阱 (v4.0已知问题)

**现象**: 搜索 `xiaohongshu.com 世界杯 Brazil` 返回的结果仅 12% 来自 xiaohongshu.com。

**根因**: Brave Search API 不支持 `site:` 操作符。`xiaohongshu.com` 在查询中只是一个普通关键词，匹配任何包含该字符串的页面（包括报道小红书的新闻网站）。

**当前修复** (尚未实施): 在 `xiaohongshu_collector.py` 的 `search_xiaohongshu()` 中增加 URL 后过滤:

```python
results = []
for item in data.get("web", {}).get("results", []):
    url = item.get("url", "")
    if 'xiaohongshu.com' not in url:
        continue  # 仅保留真实小红书页面
    results.append({...})
```

**替代方案**:
1. 使用 Google CSE (Programmable Search Engine) 过滤域名 — 需要额外API key
2. 使用 RSSHub xiaohongshu/user 路由 — 需Cookie，仅限特定用户
3. 保持现状但改名为"中文网络情感"通道而非"小红书"通道

## 情感信号强度分析

| 模型层 | ELO范围 | 占平均ELO比例 | 预测影响力 |
|--------|---------|-------------|-----------|
| 伤病 | -125 ~ 0 | 6.6% | 极高 |
| 教练 | +3 ~ +59 | 3.1% | 高 |
| 热身赛 | -37 ~ +40 | 2.1% | 中 |
| 情感(双通道) | -8 ~ +2 | 0.4% | 极低 |

`load_news_sentiment()` 中的换算公式: `adj = int(round(impact * 250))`
- impact范围: -0.15 ~ +0.05 (来自 `get_sentiment_impact()`)
- 换算后: -37 ~ +12 ELO

实际测试中48队全部落在 -8 ~ +2 区间，因为 `get_sentiment_impact()` 的阈值设计使得大多数球队得0分（中性）。

**调优方向**:
- 方案A: 接受弱信号，保持轻量
- 方案B: 将换算系数从250改为500，范围变为 ±80 ELO
- 方案C: 调整 `get_sentiment_impact()` 阈值，降低变中性所需的分数门槛

## 中文情感词典设计原则

当前60+关键词分为:
- 状态/实力: 状态火热、势不可挡、溃不成军...
- 球员/阵容: 伤愈复出、核心缺阵、赛季报销...
- 前景/预测: 夺冠热门、凉了、一轮游...
- 教练/战术: 用兵如神、战术混乱...
- 团队氛围: 内讧、众志成城、更衣室危机...
- 社区热度: 热搜、刷屏、出圈...

添加新词时遵循:
1. 必须是足球语境常用词（避免"争议"这种太泛的词）
2. 区间2-8字（太短误匹配，太长罕见）
3. 新增关键词后在 `__main__` 测试块添加验证用例
