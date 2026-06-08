# 小红书集成架构 v4.0 (2026-06-08)

## 设计决策

小红书没有原生 RSS，RSSHub 仅有一条 `/xiaohongshu/user` 路由（需 Cookie，且无搜索能力），因此采用三层采集架构：

| 层 | 方案 | 状态 |
|---|------|------|
| RSSHub user 路由 | 需 Cookie，跳过 | ❌ 未启用 |
| Brave Search API | 关键词搜索 xiaohongshu.com | ✅ 主通道 |
| web_search 兜底 | Hermes 内置 Brave 搜索 | 备用 |

## 数据流

```
xiaohongshu_collector.py          sentiment_analyzer.py        wc2026_predict.py
(Brave Search API)                (中英文双通道)               (双通道融合)
     │                                  │                           │
     ├─ search "xiaohongshu.com        │                           │
     │   世界杯 {team}"                │                           │
     ├─ store → xhs_sentiment_         │                           │
     │   cache.json                    │                           │
     │                                  │                           │
     │                    XHSConnector ←┤                           │
     │                    .fetch_football_news()                    │
     │                                  │                           │
     │                    analyze_text_cn() ──→ zh_positive/        │
     │                    (子串匹配)          zh_negative           │
     │                                  │                           │
     │                                  ├── xhs_scores ───────────→ │
     │                                                              │
     │                    RSSConnector ← DB ──→ rss_scores ───────→ │
     │                                                              │
     │                                             fusion:          │
     │                                             RSS*0.5 + XHS*0.5│
     │                                             → ±40 ELO        │
```

## Brave Search API 陷阱

### `site:` 操作符 → 422
```
GET /search?q=site:xiaohongshu.com+世界杯  → 422 Unprocessable Entity
```
**原因**: Brave Search 不支持 Google 风格的 `site:` 域名过滤。

**正确做法**: 将域名作为普通关键词加入查询。
```
GET /search?q=xiaohongshu.com+世界杯+阿根廷  → 200 OK
```

### `search_lang=zh` → 422
```
GET /search?q=xiaohongshu+世界杯&search_lang=zh  → 422
```
**原因**: `search_lang` 参数与某些查询词组合触发后端校验失败。

**正确做法**: 不传 `search_lang`，依赖 `country` 默认值 (us) 即可，中文查询仍正常返回结果。

### 速率限制
免费层 ~1 req/s。采集脚本内置 `time.sleep(1.2)` 延迟。

### 配额
免费层 2000 次/月。采集 48 队 + 关键词约 400 次。

## 中文情感分析设计

### 为什么不用分词？
中文分词需要 jieba 等库，增加依赖。当前采用子串匹配（`str.count()`），对短文本（标题+摘要）足够。

### 关键词体系
- 正向 40 词：覆盖状态/阵容/前景/战术/氛围/热度 6 个维度
- 负向 42 词：覆盖状态/伤病/前景/战术/氛围/外部 6 个维度
- 强度修饰词 10 个：影响最终分数倍率

### 归一化
`score / max(hits * 0.3, 1)` — 多个关键词命中时不会无限累积，上限锁在 ±1.0。

### 已知局限
- 子串匹配可能误命中：如"伤病"出现在"伤病已恢复"中 → 待优化
- 社区语气词（"笑死"、"绝了"）未建模 → 需语料积累后迭代
