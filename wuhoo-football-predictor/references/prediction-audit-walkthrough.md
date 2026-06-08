# 单场预测审计走查方法论

> 当用户要求解释"预测结果是怎么来的"时，按本文档的 6 层框架逐层展示，每层给出：数据来源 → 计算公式 → 数值 → 解读。

## 走查结构

每层必须回答三个问题：
1. **从哪来** — 哪个数据文件/哪个函数
2. **怎么算** — 公式/规则/逻辑
3. **意味什么** — 为什么这个调整是合理的

## 六层走查模板

### 第 1 层 — ELO 基础
- 数据：`data/elo_ratings.json`
- 公式：`P(win) = 1/(1+10^(-elo_diff/400))`
- 解读：这是"纯实力差距"，没有任何调整。告诉用户这个 ELO 差意味着什么档次（<50 同级、50-150 梯队差、150-300 明显差距、>300 碾压）

### 第 2 层 — 伤病
- 数据：`data/injuries.json`
- 逻辑：逐球员列出姓名/位置/状态/伤情/ELO惩罚
- 解读：说明每个伤员在球队中的角色（核心/主力/替补），总 penalty 对球队意味着什么。如果无伤病，明确说"双方阵容齐整"。

### 第 3 层 — 教练/磨合
- 数据：`data/team_metadata.json`
- 公式：
  - coach = 世界杯执教次数 × 8
  - result = 历史最佳战绩对应分（冠军 25→小组赛 0）
  - stability = (阵容保留率 - 0.5) × 40
  - chemistry = (近 10 场胜率 - 0.5) × 30
- 解读：解释为什么某队 coaching 得分高（如 Croatia 的 Dalic 有多届 WC 经验），或某队 chemistry 低（大量新人）

### 第 4 层 — Venue
- 数据：`data/venues.json`
- 规则：
  - 东道主 +60、南美队 +15（小组阶段）
  - 高原惩罚 = (海拔-500)/1000 × 40（仅对非适应队）
  - 高温惩罚 = (温度-28)/5 × 10（室内×0.5）
- 解读：这通常是**最大的单一调整因子**。特别强调极端 venue（Azteca 2200m 扣 68 分 ≈ 扣掉了一个梯队）

### 第 5 层 — 新闻情感
- 数据：`wuhoo-news-rss/data/news.db`，查询 `category='足球'`
- 前提：RSS cron 正常运行，DB 中有 14 天内的文章
- 诊断命令：
```bash
sqlite3 ~/wuhoo-workspace/skills/wuhoo/wuhoo-news-rss/data/news.db \
  "SELECT MAX(pub_date) FROM articles WHERE category='足球'"
```

### 第 6 层 — 手动调整
- 来源：用户 `--adj "Team:+N"`

## 汇总表

```
              Base    伤病   教练   Venue   新闻   手动   Effective
Team A:       XXXX   + XX   + XX   + XX   +  X   +  X  =  YYYY
Team B:       XXXX   + XX   + XX   + XX   +  X   +  X  =  YYYY
                                                      ─────
有效 ELO 差:                                           ±ZZZ
```

## Poisson 预测

- 公式：`lambda = max(0.2, 1.45 × 10^(elo_diff/500))`
- 遍历 0-6 进球的所有组合，求和得到胜/平/负概率
- **注意**：当 |elo_diff| > 300 时，lambda 会给出不切实际的极高 xG。此时应提醒用户关注概率方向而非具体比分。

## 判定理由模板

1. 实力面 — ELO 差距
2. 环境面 — 主场/高原/高温（如果有显著影响）
3. 人员面 — 伤病（如果有）
4. 调整面 — 多层调整后 ELO 差的变化（如果与原始差 >30）

## 参考案例

本 session 中完成了 Group A Matchday 1 完整走查——Mexico vs South Africa（碾压局+高原主场）和 South Korea vs Czech Republic（均势对抗）的对比分析，是这两种典型预测场景的最佳参考。
