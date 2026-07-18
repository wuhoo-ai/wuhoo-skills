# WC2026 预测报告增强计划

## 目标
增强 2026 世界杯预测系统，生成一份包含 48 队简介、分组分析、出线预测和淘汰赛比分预测的综合中文报告。

## 当前状态

### 已有能力 ✅
- 12 组 48 队分组数据（FIFA 2025-12 抽签）
- 55 队 ELO 评分（clubelo.com，2100-scale）
- 16 球场 Venue 数据库（含海拔/温度惩罚）
- FIFA 官方 R32 对阵表 + 第三名约束分配算法
- 10,000 次 Monte Carlo 全流程模拟
- KO 阶段概率化平局打破（非确定性强队胜）
- 基础概率输出（冠军/四强/八强/小组出线 %）
- 回测系统（WC2022 57.8%，Euro2024 51.0%）

### 缺失能力 ❌
1. **无球队简介** — 只有名字和 ELO，没有 FIFA 排名、地区、世界杯历史、近期战绩
2. **无分组叙事分析** — 只有出线概率%，没有文字分析（谁是热门、黑马、看点）
3. **无淘汰赛比分预测** — 只输出各阶段概率，不输出单次模拟的具体比分
4. **无"最可能 bracket"** — Monte Carlo 只输出聚合概率，没有展示一条"最可能路径"
5. **报告仅 JSON** — 没有人类可读的中文 Markdown 报告
6. **小组赛无 venue 建模** — 仅 KO 阶段有 venue 惩罚（已知限制 #7）
7. **ELO 数据中的球队名不匹配** — 代码用 'South Korea'，ELO JSON 也是 'South Korea'，但团队简介添加时可能引入名称差异

## 方案

### 新增文件
```
data/team_profiles.json          # 48队元数据（FIFA排名、地区、世界杯历史）
data/wc2026_expected_bracket.json # 最可能淘汰赛路径（首次模拟结果）
```

### 修改文件
```
wc2026_predict.py                # 新增 --report 模式
```

### 变更内容

#### 1. 新增 `data/team_profiles.json`
- 48 队元数据：中文名、英文名、FIFA 排名（估）、所属大洲、世界杯参赛次数、最佳成绩、近年战绩摘要
- 为中文报告提供内容素材

#### 2. `wc2026_predict.py` 新增 `--report` 模式

##### 2a. 新增函数：`simulate_one_tournament_detailed()`
- 在现有 `simulate_one_tournament()` 基础上，额外记录每场淘汰赛的比分
- 返回 `(stage_winners, group_standings, match_scores)`
- `match_scores` 结构：`{stage: {slot: (team_a, team_b, score_a, score_b)}}`

##### 2b. 新增函数：`run_monte_carlo_with_expected(n_sims)`
- 运行完整 MC 模拟
- 额外：首次模拟作为"最可能路径"记录所有比分
- 返回 `(stats, expected_bracket, expected_scores)`

##### 2c. 新增函数：`generate_report(stats, expected_scores, team_profiles)`
- 输出中文 Markdown 报告
- 包含：
  - **赛事概览**：48 队 / 12 组 / 16 球场
  - **48 队实力排行**：ELO 排名表（前 20 详细，其余列表）
  - **分组分析**：每个小组：
    - 球队列表（ELO + FIFA 排名）
    - 出线概率表（前 2 + 第 3 名 %）
    - 一两句分析（"热门：XX，黑马：XX"）
  - **淘汰赛路径预测**：
    - R32 → R16 → QF → SF → Final → Champion 完整对阵表
    - 每场比赛的具体比分预测
  - **概率总结**：冠军/四强/八强概率表
- 保存为 Markdown 文件 `data/wc2026_report_YYYYMMDD.md`

##### 2d. 新增函数：`analyze_group(profiles, elo_data, advance_probs, pts_avg)`
- 为每个小组生成分析文本
- 规则：ELO 差距 > 100 标记"绝对热门"；第2和第3 ELO < 20 "死亡之组"
- 结合 venue 因素（A 组 Mexico 主场优势等）

#### 3. 小组赛 venue 建模（修复已知限制 #7）
- 在小组赛模拟中加入 venue 信息
- 需要先查 2026 世界杯小组赛赛程 → 每个 venue 分配
- **这步复杂度高**（需要完整赛程数据），可在后续 PR 中完成
- **本计划先采用简化版**：主队优势（USA/Mexico/Canada +60, 南美 +15）

### 不做的
- 不修改现有 Monte Carlo 核心逻辑（保持稳定）
- 不修改 `fp_predict.py` 通用 CLI
- 不修改 `prediction_models.py` 模型类
- 不接入实时新闻情绪（需要 RSS 数据库配合，用户当前用微信手动触发）
- 不修改现有测试（不引入 breaking changes）

### 执行步骤

1. **创建 `team_profiles.json`** — 48 队元数据
2. **修改 `wc2026_predict.py`**：
   - 提取 `print_results_level()` 函数
   - 新增 `simulate_one_tournament_detailed()`
   - 新增 `run_monte_carlo_with_expected()`
   - 新增 `generate_report()`
   - 新增 `analyze_group()`
   - main() 中新增 `--report` 参数处理
3. **运行 `python3.11 wc2026_predict.py --report`** — 生成完整报告
4. **将报告发到微信**

### 风险与权衡
- 新增的 `team_profiles.json` 是静态数据，需要手工维护（但数据不常变）
- "最可能路径"只取 1 次模拟，有随机性（可考虑取出现频率最高的完整路径，但复杂度高）
- 不支持 `--sims` 重定向（报告模式下固定运行 10,000 次）
- 英文队名不影响中文报告（team_profiles 有中文映射）
