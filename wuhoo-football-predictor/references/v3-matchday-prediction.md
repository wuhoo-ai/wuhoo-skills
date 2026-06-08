# v3.0 比赛日单场预测 — 设计参考

> 摘自 2026-06-02 迭代计划，供后续开发参考。
> 完整计划: ~/.hermes/plans/2026-06-02_142900-wc2026-v3-matchday-prediction.md

## 目标

一条命令 → 最新数据 → 完整推理 → 预测结果 + 审计链路。
世界杯小组赛每场比赛可手动触发，输出 90 分钟胜/平/负概率 + 最可能比分 + 完整推理。

## CLI 接口设计

```bash
# 按对阵预测
python3.11 wc2026_predict.py --match "Argentina" "France"

# 按赛程编号预测
python3.11 wc2026_predict.py --match-id 15

# 按小组+轮次预测（自动查赛程）
python3.11 wc2026_predict.py --group A --matchday 1

# 带新闻情感
python3.11 wc2026_predict.py --match "Argentina" "France" --news

# 主观调整覆盖
python3.11 wc2026_predict.py --match "Argentina" "France" --adj "Argentina:-10"

# 输出到 Markdown 文件
python3.11 wc2026_predict.py --match "Argentina" "France" -o prediction.md
```

## 赛程数据文件 Schema

```json
// data/wc2026_schedule.json
{
  "generated": "2026-06-02",
  "source": "FIFA Official / Yahoo Sports",
  "matches": [
    {
      "match_id": 1,
      "date": "2026-06-10",
      "time_beijing": "22:00",
      "group": "A",
      "matchday": 1,
      "team_a": "Mexico",
      "team_b": "South Africa",
      "venue": "Estadio Azteca"
    }
  ]
}
```

共 12 组 × 3 轮 × 2 场 = 72 场比赛。

## 预测输出结构

每场预测必须依次展示以下审计链路：

1. **基本信息**: 日期、开球时间、球场
2. **ELO 基础对比**: 双方 ELO 值 + 来源 + 基础胜率
3. **伤病调整**: 逐球员列出 penalty + 球队总扣分
4. **教练/磨合调整**: coach + result + stability + chemistry 明细
5. **Venue 影响**: 海拔/高温惩罚 + 主场优势
6. **新闻情感**: 最近 7 天情感调整（如启用）
7. **综合 ELO**: base + 所有调整后的有效 ELO 差
8. **Poisson 预测**: 胜/平/负概率 + 最可能比分
9. **综合判定**: 预测结果 + 置信度 + 理由

## 预测历史

```jsonl
// data/prediction_history.jsonl
{"timestamp": "2026-06-10T21:00:00+08:00", "team_a": "Mexico", "team_b": "South Africa", 
 "prediction": {"home_win": 0.923, "draw": 0.051, "away_win": 0.026}, 
 "predicted_winner": "Mexico", "actual": null}
```

## 数据刷新管线

预测前自动运行 `scripts/pre_match_refresh.py`（待实现）：
1. 尝试刷新 ELO（调用 fetch_elo.py）
2. 打印伤病摘要（提醒手动检查）
3. 打印元数据覆盖度
4. 验证所有数据完整性
5. 输出数据时效性报告

可用 `--no-refresh` 跳过。
