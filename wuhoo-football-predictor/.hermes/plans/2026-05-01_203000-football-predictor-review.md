# Football Predictor — 2026 World Cup 整体 Review & 补齐计划

> 日期: 2026-05-01 | 状态: 未完成，需要大量工作
> 回测基线: WC2022 56.2%, Euro2024 52.9% (高于随机 33%)

---

## 一、现状总评

### ✅ 已完成部分

| 模块 | 文件 | 状态 |
|------|------|------|
| 预测模型栈 | `scripts/prediction_models.py` (Poisson + Elo + Factor + Ensemble) | ✅ 架构完整 |
| 回测引擎 | `scripts/backtest.py` | ✅ WC2022 64场 / Euro2024 51场 |
| 数据获取 | `scripts/fetch_data.py` + `scripts/download_data.py` | ✅ 8024场国际比赛 (2018+) |
| 情感分析 | `scripts/sentiment_analyzer.py` + RSSConnector | ✅ 对接 wuhoo-news-rss |
| CLI 入口 | `fp_predict.py` | ✅ --predict/--backtest/--analyze |
| WC2026 原型 | `wc2026_predict.py` | ⚠️ 独立脚本，未集成到主系统 |
| 历史数据 | `data/worldcup_2022_full.csv` (64场), `data/euro_2024_full.csv` (51场) | ✅ |

### ❌ 核心问题：两套系统分裂

存在两条完全独立的代码路径，ELO 尺度不兼容：

| 维度 | `fp_predict.py` + `scripts/` | `wc2026_predict.py` |
|------|------------------------------|---------------------|
| ELO 尺度 | Argentina = 1859 (clubelo 混合) | Argentina = 2114 (国家队专用) |
| Poisson 公式 | `PoissonModel.predict(lambda_a, lambda_b)` | `predict_score(elo_diff)` 自实现 |
| 因子模型 | FactorModel 6因子加权 | 无因子模型 |
| 情感分析 | 集成 (mock_news_for_team) | 未集成 |
| 小组赛模拟 | 无 | Monte Carlo 3000次 |
| 淘汰赛 | 无 | 确定性（最高 ELO 晋级） |
| CLI 集成 | ✅ | ❌ 独立脚本 |

---

## 二、WC2026 预测 — 差距清单

### 🔴 P0 — 阻塞性问题

#### 1. 分组数据不完整
- **Group D 第4队**: 当前用 `Team_D4` (Elo=1685) 占位
- 实际：FIFA 洲际附加赛尚未结束，南美/亚洲/非洲/大洋洲各1个名额待定
- 影响：Group D 预测无效，32强名单不完整
- **行动**: 定期关注 FIFA 资格赛结果，动态更新 GROUPS dict

#### 2. 淘汰赛对阵表是假的
- `wc2026_predict.py` 使用「相邻配对」：`teams[0] vs teams[1], teams[2] vs teams[3]...`
- FIFA 官方 2026 世界杯有预定义的 R32 对阵表（小组第1 vs 小组第3/第2 等交叉配对）
- **影响**: 整个淘汰赛预测无效 — Argentina 在 R16 就碰 Brazil 在现实中几乎不可能
- **行动**: 实现 FIFA 官方 R32 bracket 填写逻辑

#### 3. ELO 尺度不一致
- 两套 ELO 相差约 250 分，导致 Poisson lambda 计算完全不同
- `wc2026_predict.py` 的 lambda 公式 `base=1.45 * 10^(elo_diff/500)` 需要校准
- **行动**: 统一 ELO 数据源，用真实比赛比分校准 lambda 参数

### 🟡 P1 — 预测质量缺陷

#### 4. 淘汰赛无 Monte Carlo
- 当前淘汰赛是确定性的：每组按 advance_probs 排名取前2 + 8个最佳第3
- 没有模拟小组赛的随机性（爆冷、积分相同、净胜球）
- **行动**: 将 Monte Carlo 扩展到淘汰赛阶段，10000次全流程模拟

#### 5. 最佳第3名规则过于简化
- 当前: 按 `pts_avg > ELO` 排序
- 实际 FIFA 规则: Points > Goal Difference > Goals For > Fair Play > Drawing of lots
- **行动**: 实现完整的 12组第3名比较逻辑

#### 6. 缺乏主场/地理因素
- 2026 世界杯在 3 个国家 16 个城市举办
- 关键因素:
  - 海拔: Mexico City (2200m), Guadalajara (1566m) → 对欧洲球队影响显著
  - 气候: 6-7月美国南部高温 (Miami, Houston, Dallas) vs 北部温和
  - 旅行距离: 跨赛区比赛增加疲劳
- 当前只有简单的主场加成 (USA/Mexico/Canada +60)
- **行动**: 建立 venue 数据库，按海拔/温度/旅行距离调 ELO

#### 7. 无实时新闻情绪集成
- `wc2026_predict.py` 完全不使用 `sentiment_analyzer.py`
- 赛前 2 周的伤病/状态新闻应显著影响预测
- **行动**: 在 Monte Carlo 模拟中注入新闻情绪因子

### 🟢 P2 — 工程完善

#### 8. CLI 未接入 WC2026
- `fp_predict.py` 不支持 `--tournament wc2026`
- **行动**: 添加 `wc2026` 模式，统一 CLI 入口

#### 9. 代码重复严重
- Poisson PMF 在 `prediction_models.py` 和 `wc2026_predict.py` 中各自实现
- ELO 预测逻辑也在两处
- **行动**: `wc2026_predict.py` 改为 import `prediction_models` (需先统一 ELO)

#### 10. 测试不匹配
- `test_football.py` 使用 `win_a`/`win_b` 字段，但 `prediction_models.py` 输出 `home_win`/`away_win`
- `EnsembleModel` 测试签名 `predict(predictions)` 与实际不符 (实际接收独立参数)
- **行动**: 修复测试

#### 11. 无自动化报告
- 当前仅有 console 输出和手动生成的 `data/wc2026_prediction_report.md`
- **行动**: 实现 JSON/HTML/Markdown 自动报告生成

#### 12. 无定时更新
- ELO 数据 15 天未更新 (4月22日)
- **行动**: 建立 cron job，每周更新 ELO，赛前 2 周每日更新

---

## 三、建议执行路线 (按优先级排序)

### Phase 1: 基础设施修复 (1-2 天)
```
1.1 统一 ELO 数据源 → 使用 fetch_elo.py 的 2100-scale
1.2 校准 Poisson lambda 参数 (用 WC2022 真实比分回测)
1.3 修复 tests/test_football.py 字段名不匹配
1.4 wc2026_predict.py 改为 import prediction_models
1.5 Group D 第4队 ← 查 FIFA 最新资格赛结果
```

### Phase 2: 预测质量提升 (2-3 天)
```
2.1 实现 FIFA 官方 R32 knockout bracket 映射
2.2 完整 Best 3rd Place 排名规则
2.3 Monte Carlo 扩展到淘汰赛 (10,000 sims)
2.4 Venue 数据库 (16个比赛城市，含海拔/气候/时区)
2.5 海拔/旅行距离 ELO 调整
```

### Phase 3: 集成与自动化 (1-2 天)
```
3.1 fp_predict.py 添加 --tournament wc2026
3.2 新闻情绪接入 Monte Carlo 模拟
3.3 自动化报告生成 (JSON + MD + HTML)
3.4 Cron job: 每周 ELO 更新
3.5 更新 SKILL.md 文档
```

### Phase 4: 增强 (可选)
```
4.1 真实赔率对比 (Bet365/OddsAPI)
4.2 阵容/伤病数据库
4.3 交互式 Web dashboard
4.4 实时比赛日更新
```

---

## 四、文件改动清单

| 文件 | 改动类型 | 说明 |
|------|----------|------|
| `scripts/fetch_elo.py` | 修改 | 统一 ELO 输出格式，与 wc2026_predict 兼容 |
| `wc2026_predict.py` | 重构 | Import prediction_models, 统一 ELO, 修复 bracket |
| `scripts/prediction_models.py` | 修改 | 添加 tournament bracket 逻辑, Poisson λ 校准 |
| `fp_predict.py` | 新增 | 添加 wc2026 赛事模式 |
| `tests/test_football.py` | 修复 | 字段名 win_a→home_win, 修复 EnsembleModel 签名 |
| `SKILL.md` | 更新 | 添加 WC2026 完整文档 |
| `data/venues.json` | 新建 | 16个比赛城市 database |
| `data/groups.json` | 更新 | 补全 Group D 第4队 |
| `configs/bracket.json` | 新建 | FIFA 官方 R32 对阵表 |
| `.hermes/cron/` | 新建 | ELO 每周更新 job |

---

## 五、风险与开放问题

1. **FIFA 最终名单**: 各队 26 人大名单要到 2026 年 5 月底才公布，在此之前所有预测基于 ELO + 历史数据
2. **ELO 数据源可靠性**: clubelo.com 国家队数据更新频率不稳定，建议增加 eloratings.net 作为备份
3. **伤病不可预测**: 世界杯前 1 个月是伤病高发期，新闻情绪权重应在赛前 2 周动态调高
4. **回测准确率上限**: WC2022 56.2% 已接近足球预测的学术上限 (~60%)，进一步提升空间有限
5. **Venue 气候数据**: 需要获取各城市 6-7 月历史天气数据，来源待确认

---

## 六、数据统计

```
总代码文件:      8 个 Python 脚本 + 1 CLI + 1 WC2026独立脚本
总代码行数:      ~1,600 行
测试文件:        1 个 (test_football.py, 226行, 部分断言不匹配)
数据文件:        6 个 CSV (8,024 国际比赛 + 64 WC2022 + 51 Euro2024 + 其他)
配置文件:        3 个 JSON (tournaments, weights, rss_sources)
ELO 数据:        45 支球队 (dated 2026-04-22, 15 days stale)
已有报告:        1 份 WC2026 预测报告 (2026-04-23, 存在已知缺陷)
RSS 新闻源:      9 个, 其中 3 个可用 (BBC/Sky/Guardian), 6 个不可用
```
