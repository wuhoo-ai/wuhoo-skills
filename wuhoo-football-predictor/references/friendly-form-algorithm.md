# 热身赛近期状态因子 — 算法设计文档

## 动机

世界杯前（~3周窗口）各队会打 1-3 场热身赛。这些比赛结果包含重要的近期状态信号，但传统 ELO 更新滞后（上次更新 5月21日，世界杯 6月11日开幕）。此因子填补 ELO 更新滞后与赛前状态变化之间的信息缺口。

## 数据流

```
Agent 采集 → friendly_matches.json → fetch_friendlies.py --compute-form
                                         ↓
                                   friendly_form_adjustments.json
                                         ↓
                              wc2026_predict.py (第4.5层)
```

## 算法

### 输入

- `elo_ratings.json`: 球队 ELO 评分
- `friendly_matches.json`: 比赛列表，每条包含 team_a, team_b, score_a, score_b, date, neutral

### 步骤

1. **计算 ELO 期望胜率**

```
expected_a = 1 / (1 + 10^(-(elo_a - elo_b + home_adv) / 400))
```
- home_adv = 60（非中立场地），0（中立场地）

2. **计算实际结果偏差**

- 胜: `actual_diff > 0`
- 平: `actual_diff == 0`
- 负: `actual_diff < 0`

3. **映射到 ELO 调整**

核心思想：结果越出乎意料，调整越大。

| 期望 | 实际 | 基础调整 | 说明 |
|------|------|:---:|------|
| 高胜率(>80%) | 赢了 | +0~+8 | 符合预期 |
| 高胜率(>80%) | **输了** | **-30~-40** | 重大冷门 |
| 低胜率(<30%) | 赢了 | +30~+40 | 重大惊喜 |
| 低胜率(<30%) | 输了 | -8~0 | 符合预期 |
| ~50% | 赢了 | +15~+20 | 惊险取胜 |
| ~50% | 输了 | -15~-20 | 惜败 |
| 任何 | 平局 | ±15 | 取决于哪边更强 |

净胜球加成: 赢3球以上额外+10，赢2球额外+5（对称惩罚输球方）

4. **时间衰减加权**

```
weight = 0.9 ^ (days_ago / 7)
```

- 当天: weight=1.0
- 7天前: weight=0.9
- 14天前: weight=0.81
- 21天前: weight=0.73

5. **最终调整**

```
weighted_avg = Σ(adj_i × weight_i) / Σ(weight_i)
raw_avg = Σ(adj_i) / n
final = round(0.7 × weighted_avg + 0.3 × raw_avg)
```

混合加权平均和原始平均（30%），防止单场近期比赛过度主导。

## 真实案例

### France vs Ivory Coast (2026-06-04)

```
ELO: France 2082 vs Ivory Coast 1676
主场优势: +60 (南特)
期望胜率: 1/(1+10^(-(2082-1676+60)/400)) = 1/(1+10^(-466/400)) = 93.5%
实际结果: France 1-2 Ivory Coast (法国输了)
→ 大冷门: -37 ELO (France), +37 (Ivory Coast)
```

### England vs Sweden (2026-05-30)

```
ELO: England 2020 vs Sweden 1719
期望胜率: ~85%
实际结果: England 2-4 Sweden
→ 冷门: -35 ELO (England), +35 (Sweden)
净胜球加成(+2球): +5 额外
```

## 局限性

1. **热身态度差异**: 强队可能轮换或演练战术，热身赛结果不完全反映正式比赛状态
2. **样本量小**: 每队 1-3 场，统计显著性不足
3. **手动采集**: 依赖 Agent 搜索+录入，覆盖不及时
4. **单一维度**: 只看比分，不考虑阵容轮换、战术试验等背景

## 未来改进方向

- 热身赛净胜球回归: 用进球差而非胜负二分类
- 对手实力二次加权: 区分"对强队平局"vs"对弱队大胜"
- 自动采集: RSS/API 管线（ESPN API 或 FIFA 官方 feed）
- 考虑换人次数: 热身赛换人多→结果信号更弱
