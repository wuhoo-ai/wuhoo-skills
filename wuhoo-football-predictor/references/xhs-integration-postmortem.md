# XHS (小红书) 集成事后分析 — v4.0 → v4.1 回退记录

## 背景
2026-06-08，用户要求将小红书作为世界杯预测系统的中文社区数据源。小红书是2026世界杯中国持权转播商（1亿+足球用户）。

## 架构 (v4.0)
三层：RSSHub Cookie路由 + Brave Search采集 + 中文情感分析 → 双通道融合(RSS 50% + XHS 50%)

## 实测结果
Brave Search API 不支持 `site:` 域名过滤。查询 "xiaohongshu.com 世界杯 Brazil" 返回8条结果中仅1条(12%)来自xiaohongshu.com，其余为Wikipedia/CCTV/Facebook/163.com等。

## 用户决策
"去掉XHS采集吧，看起来没什么太大用" — 12%命中率 + 情感信号(-8~+2 ELO)远小于伤病(-125~0)和教练(+3~+59)

## 回退 (v4.1)
- wc2026_predict.py → RSS单通道
- xhs_sentiment_cache.json → 删除
- xiaohongshu_collector.py → DEPRECATED
- 中英文情感分析器保留（未来若有真正XHS数据源可快速恢复）

## 教训
1. 先验证数据质量再集成（域名过滤能力不是搜索API标配）
2. 情感信号对预测影响天然弱，不应期望显著改变结果
3. 务实优先：快速验证→快速回退
