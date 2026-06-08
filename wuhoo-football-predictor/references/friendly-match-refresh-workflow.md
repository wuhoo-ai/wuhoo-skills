# 热身赛数据刷新工作流 — v4.1 实战记录

## 触发条件
世界杯前（5月底-6月初），各队进行热身赛。需从英文媒体采集比分并录入系统。

## 数据源
1. **football365.com** — 最全面的热身赛汇总：https://www.football365.com/news/world-cup-2026-warm-up-friendly-fixtures-results-kick-off-times-what-tv-channel
2. **BBC Sport** — 重点比赛报道
3. **ESPN** — 球队页面含比赛结果
4. **StrikerReport** — 分析文章含结果表格

## 批量采集流程

### Step 1: web_extract 获取汇总页面
```
web_extract("https://www.football365.com/news/world-cup-2026-warm-up-friendly-fixtures-results-kick-off-times-what-tv-channel")
```

### Step 2: 交叉验证
用 web_search 确认关键比赛比分（Argentia/Portugal/Scotland等）

### Step 3: 批量录入
```bash
cd ~/wuhoo-workspace/skills/wuhoo/wuhoo-football-predictor

# 单场格式
python3.11 scripts/fetch_friendlies.py --add '{"team_a":"Argentina","team_b":"Honduras","score_a":2,"score_b":0,"date":"2026-06-07"}'

# 批量录入用Python循环
python3.11 -c "
import subprocess, json
matches = [
    {'team_a':'Argentina','team_b':'Honduras','score_a':2,'score_b':0,'date':'2026-06-07'},
    # ... more matches
]
for m in matches:
    subprocess.run(['python3.11','scripts/fetch_friendlies.py','--add',json.dumps(m)])
"
```

### Step 4: 重新计算状态调整值
```bash
python3.11 scripts/fetch_friendlies.py --compute-form
```

## v4.1 采集成果
- 从 27场 → 46场 (+22场新增)
- 覆盖率: 15队 → 47/48队 (仅 Uruguay 无公开热身赛)
- 关键新增: Scotland +20, Argentina +8, Portugal +7, Iran +22

## 注意事项
- `fetch_friendlies.py --add` 自动去重（同日期+同对阵不会重复添加）
- 日期格式: YYYY-MM-DD
- 球队名必须使用规范英文名（与 GROUPS 定义一致）
- 比分必须是最终结果，TBD状态不要录入（会跳过计算）
- 不是所有48队都有公开热身赛（Uruguay未安排，正常现象）
