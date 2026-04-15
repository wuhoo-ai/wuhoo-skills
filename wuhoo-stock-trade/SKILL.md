---
name: wuhoo-stock-trade
description: "多市场交易执行（Workflow C）。接收选股结果、分析结果、辩论结果作为输入，执行风控检查、交易模拟/实盘、复盘。wuhoo 冠名 skill 为 OpenClaw 企业级关键 skill，需重点维护。"
metadata: { "openclaw": { "emoji": "⚡", "requires": { "bins": ["python3"] } } }
---

# wuhoo-stock-trade — 多市场交易执行（Workflow C）

> **⚠️ 企业级关键 Skill**
> 以 `wuhoo-` 冠头的 skill 是当前 OpenClaw 系统的**企业级关键 skill**，承担核心业务价值。
> 这些 skill 的代码质量、稳定性和可维护性需要特别关注。

## 功能概述

接收上游 Skill 的输出作为输入，执行风控检查、交易模拟/实盘操作、每日复盘。是量化交易的**执行引擎**，不再包含选股过程。

**输入依赖**：
1. 选股结果（来自 `wuhoo-stock-pick`）
2. 多维度分析结果（来自 `wuhoo-stock-deep-analysis`）
3. 多空辩论结果（来自 debate agent）

## 完整流程

```
输入: 选股结果 + 分析结果 + 辩论结果
    ↓
Step 1: 投资建议整合（综合上游结果）
    ↓
Step 2: 风控检查 + 人工审批
    ↓
Step 3: 交易执行（模拟盘 / 实盘）
    ↓
Step 4: 每日复盘报告
    ↓
审计报告 (Audit)
```

## 市场支持

| 市场 | 代码 | 数据源 | 交易执行 |
|------|------|--------|----------|
| A股 | CN | Tushare | 富途 OpenAPI (OpenCNTradeContext) |
| 港股 | HK | 富途 OpenAPI | 富途 OpenAPI (OpenHKTradeContext) |
| 美股 | US | yfinance | 富途 OpenAPI (OpenUSTradeContext) |

## 输入格式约定

### 选股结果 (`01_selected_stocks.json`)

```json
[
  {
    "ts_code": "00700.HK",
    "name": "腾讯控股",
    "score": 0.85,
    "factors": {"volatility": 0.12, "momentum_5d": 3.2}
  }
]
```

### 分析结果 (`02_analysis_results.json`)

每只股票的技术面/基本面/舆情面分析。

### 辩论结果 (`03_debate_results.json`)

```json
{
  "00700.HK": {
    "bull_score": 8,
    "bear_score": 3,
    "consensus": "BUY",
    "confidence": 0.75
  }
}
```

## 使用方式

```bash
cd ~/.openclaw/workspace/agents/trade
source venv-futu/bin/activate

# A股分析（推荐：跳过交易执行，仅分析）
python workflow_c_multi_market.py --market cn --date 2026-04-09 --skip-trades

# 港股完整流程（含交易执行）
python workflow_c_multi_market.py --market hk --date 2026-04-09

# 美股分析
python workflow_c_multi_market.py --market us --date 2026-04-09 --skip-trades

# 启用人工审批（大额交易需要确认）
python workflow_c_multi_market.py --market hk --with-approval
```

### 参数说明

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--market` | 市场 (cn/hk/us) | HK |
| `--date` | 交易日期 (YYYY-MM-DD) | 今天 |
| `--skip-trades` | 跳过交易执行 | False |
| `--with-approval` | 启用人工审批 | False |
| `--skip-review` | 跳过每日复盘 | False |

## 输出目录

```
~/.openclaw/workspace/agents/trade/data/workflow_c/{MARKET}_{DATE}/
├── 01_selected_stocks.json       # 选股结果（输入）
├── 02_analysis_results.json      # 多维度分析（输入）
├── 03_debate_results.json        # 多空辩论结果（输入）
├── 04_recommendations.json       # 投资建议
├── 05_trade_results.json         # 交易执行结果（如有）
├── 05_audit_report.json          # 审计报告
├── audit_report.md               # 审计报告 (Markdown)
├── workflow_results.json         # 完整流程结果
└── workflow_analysis_report.md   # 综合分析报告
```

## 风控规则

| 规则 | 阈值 |
|------|------|
| 单股最大仓位 | ≤ 20% |
| 总仓位最低现金 | ≥ 10% |
| 单笔止损 | -8% |
| 账户总止损 | -15% |
| 大额交易确认 | > 5% 仓位需用户确认 |

## 数据质量与降级

- **Good**：使用真实市场数据
- **Degraded**：部分数据源不可用，使用估计值/降级数据
- 审计模块会记录每只股票的数据可靠性评分 (0-100)
- 可靠性低于阈值的股票会标记告警

## 依赖

```bash
cd ~/.openclaw/workspace/agents/trade
source venv-futu/bin/activate
pip install futu-api pandas
```

## 与其他 Workflow 的关系

| Skill | 对应 Workflow | 用途 |
|-------|--------------|------|
| wuhoo-stock-pick | 选股 | 多因子选股（上游输入） |
| wuhoo-stock-deep-analysis | Workflow B | 单股深度分析（上游输入） |
| wuhoo-stock-trade | Workflow C | 多市场交易执行（本 skill） |
| wuhoo-trade-diagnose | Workflow D | 持仓诊断（监督） |

---

*创建时间：2026-04-09*
*更新时间：2026-04-15*
*版本：2.0 — 剥离选股，接收上游结果，重命名 wuhoo-stock-trade*
