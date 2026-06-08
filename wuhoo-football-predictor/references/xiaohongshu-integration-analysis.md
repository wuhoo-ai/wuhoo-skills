# 小红书数据整合分析 (2026-06-08)

## 背景

2026年5月27日，小红书官宣成为2026美加墨世界杯持权转播商，104场赛事免费直播。
足球兴趣用户突破1亿，内容互动量同比增长>100%。

## 当前数据源缺口

现有7个足球RSS源全部为英文西方媒体（BBC/Sky Sports/Guardian/Football Rankings/
SoccerNews/World Soccer Talk/Football Italia），零中文源、零中国社区数据。

## RSSHub 小红书路由现状

仅有 **一条路由**: `/xiaohongshu/user/:user_id/:category`

- 功能：抓取指定用户的笔记列表或收藏
- 要求：`XIAOHONGSHU_COOKIE` 环境变量
- 限制：无搜索路由、无热榜路由、无关键词路由
- 稳定性：反爬严重，GitHub 多个 issue 反馈间歇性挂掉

## 推荐方案：三层采集架构

### 第一层：RSSHub user 路由（跟踪核心 KOL）
```
目标账号：范志毅、谢晖、小红书世界杯官方号、2-3个足球大V
数据价值：专业战术分析、伤病首发消息、一手解读
```

### 第二层：Python 关键词搜索采集
```
工具：XHS-Downloader (GitHub: JoeanAmier/XHS-Downloader, 8k+ stars)
能力：关键词搜索笔记、提取笔记内容+互动数据、无需登录
数据价值：社区讨论热度、球迷情绪、群体智慧信号
```

### 第三层：web_search 兜底
```
site:xiaohongshu.com 世界杯 {关键词}
数据价值：补充长尾信息，零成本零维护
```

## 集成到预测管线的路径

```
小红书(KOL笔记 + 关键词搜索) → SQLite news.db → zh_sentiment_analyzer → ±30 ELO
现有英文RSS → SQLite news.db → en_sentiment_analyzer → ±40 ELO
融合: 英文40% + 中文40% + 社区信号20% → 加权 ELO 调整
```

## 需要的开发工作

1. RSSHub 容器添加 `XIAOHONGSHU_COOKIE` 环境变量
2. 新增 feeds/config.yaml 小红书源（KOL用户路由）
3. 新建 `scripts/xhs_collector.py` — 关键词搜索采集脚本
4. 扩展 `sentiment_analyzer.py` — 中文情感关键词词典 + 社区信号权重
5. 修改 `wc2026_predict.py` `load_news_sentiment()` — 双通道融合

## 社区信号设计

| 信号 | 数据来源 | 预测含义 |
|------|---------|---------|
| 讨论热度比 | 两队话题笔记数比值 | 关注度不对称 |
| 情感极性 | 评论区情感分析 | 球迷共识信号 |
| 互动密度 | 点赞+评论+收藏/笔记数 | 话题热度与影响力 |
| 预测参与比 | 小红书赛事预测投票 | 群体智慧 |

## 风险

- RSSHub 小红书路由反爬：用 Cookie 认证 + 低频请求 + XHS-Downloader 备用
- 中文情感分析准确度：先用关键词词典（与英文同架构），逐步迭代
- Cookie 过期：健康检查 cron + 告警
