# 俱乐部单场预测完整案例 — 2026欧冠决赛 Arsenal vs PSG

> 生成日期: 2026-05-30 | 执行方式: execute_code (Python prediction_models.py)

## 数据采集 (8源交叉验证)

### ELO 评分
| 来源 | Arsenal | PSG | Scale | 日期 |
|------|:------:|:----:|-------|------|
| elofootball.com | 2342 | 2314 | ~2300 | 2026-05-20 |
| clubelo.com/UCL | 2066 | 1964 | ~2000 | 2026-05-24 |

→ 选用 elofootball.com (更新、scale 更合理)，归一化 -200 至 2100-scale: Arsenal 2142, PSG 2114

### 伤病/停赛
| 球员 | 球队 | 状态 | ELO扣分 | 来源 |
|------|------|:----:|:-------:|------|
| Thomas Partey | Arsenal | ⛔ 停赛 | -25 | football.london (5/29) |
| Jurrien Timber | Arsenal | ⚠️ 伤愈复出 | -10 | football.london (5/29) |
| Ousmane Dembélé | PSG | ⚠️ 随队未训练 | -10 | football.london (5/29) |
| Achraf Hakimi | PSG | ⚠️ 大腿伤 | -15 | Yahoo Sports (5/29) |

### 交锋记录 (H2H)
- 总交锋: 7次 (Arsenal 3W, PSG 2W, 2D)
- 最近3次欧冠: PSG 2W 1L (含2025半决赛总比分3-1淘汰Arsenal)
- 来源: AS USA, SI.com, UEFA.com

### 赔率市场
- PSG 4/6 (~60% implied) — The Athletic/NYTimes (5/28)
- Under 2.5 goals (-120) — FanDuel

### 赛季关键数据
- Arsenal: 9场欧冠零封 (最多), 389 passes/match, 刚夺英超
- PSG: 547 passes/match (欧冠第2), 对方禁区触球最多, Kvaratskhelia 10球/15场

## 调整计算链路

```
Arsenal:  2342 (base, elofootball)
         -200 (归一化至2100-scale)
         -35  (伤病: Partey停赛-25 + Timber伤愈-10)
         -10  (锦标赛背景: 首次决赛20年)
         -5   (近期状态: 赛季末段下滑)
         = 2092 (final)

PSG:      2314 (base, elofootball)
         -200 (归一化至2100-scale)
         -25  (伤病: Dembele-10 + Hakimi-15)
         +15  (锦标赛背景: 卫冕冠军)
         +0   (近期状态: 法甲收官轮换输球, CL状态佳)
         = 2104 (final)

Δ = +12 (PSG微弱领先)
```

## 模型输入

```python
elo_arsenal = 2092
elo_psg = 2104
goals_arsenal = 1.4  # xG估计
goals_psg = 1.8      # xG估计
is_neutral = True    # Puskás Aréna, 布达佩斯

factors = {
    'elo_diff': (2092-2104)/400,      # -0.030
    'recent_form': (-5-0)/50,          # -0.100
    'head_to_head': -0.10,             # PSG最近2场欧冠淘汰赛连胜
    'avg_goals': (1.4-1.8)/3,          # -0.133
    'news_sentiment': -0.10,           # PSG卫冕叙事占优
    'tournament_context': -0.15,       # PSG决赛经验优势
}
```

## 预测结果

| Model | Arsenal Win | Draw | PSG Win |
|-------|:-----------:|:----:|:-------:|
| Poisson (xG) | 30.0% | 22.9% | 47.0% |
| ELO | 36.4% | 24.6% | 39.0% |
| Factor | 32.8% | 24.3% | 43.0% |
| **Ensemble** | **33.4%** | **24.1%** | **42.6%** |

**推荐: PSG 客胜** (置信度: ⭐⭐ MEDIUM)
**最可能比分: 1-1 (10.3%), 1-2 (9.2%), 0-1 (7.3%)**
**冷门概率: 21.6% (Arsenal作为微弱下狗)**

## 赛后验证 (待填写)

| 项目 | 预测 | 实际 | 偏差 |
|------|------|------|------|
| 90分钟结果 | PSG 胜 42.6% | | |
| 总进球 | — | | |
| 比分 | 1-1 / 1-2 / 0-1 | | |

## 经验教训

1. **Club ELO 双源验证必要**: elofootball (Δ28) 与 clubelo/UCL (Δ102) 差异巨大，选哪个直接影响结论
2. **停赛 > 伤病**: Partey 停赛对阿森纳中场拦截影响可能被低估（最终仅 -25），实际比赛可能更显著
3. **H2H 时效性权重**: 30年前的 Cup Winners' Cup 交手 vs 去年的欧冠半决赛，后者权重应远大于前者
4. **赔率 vs 模型差距**: 市场 PSG ~60%，模型 PSG ~43% — 差距来自市场对 PSG 统治力的额外溢价
5. **xG 估计手动化**: 当前无自动化 xG 数据源，靠人工根据赛季统计估算，精度有限
