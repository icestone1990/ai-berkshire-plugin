# 财务数据源与交叉验证规范

> 本规范适用于所有涉及企业财务数据的 command。完整版见 `/financial-data` 命令。

## 铁律

**每个关键数据必须来自两个独立来源，误差 > 1% 须标记。** 严禁只引用单一数据站。

## 数据源优先级

### 美股（PDD、腾讯 ADR、网易 ADR 等）

| 优先级 | 来源 | 说明 |
|---|---|---|
| 主 | macrotrends.net | 直接访问，无需注册 |
| 副 | stockanalysis.com | 交叉验证 |
| 一手 | SEC EDGAR（sec.gov） | 10-K / 10-Q 原文 |

### A 股 / 港股

| 优先级 | 来源 | 说明 |
|---|---|---|
| 主 | 巨潮资讯网（cninfo.com.cn）/ 港交所披露易 | 一手财报 |
| 副 | 东方财富 / 同花顺 / 雪球 | 交叉验证 |

### 未上市

融资轮次（Crunchbase / IT 桔子）+ 可比上市公司 + 行业报告，多源拼凑。

## 程序化校验（必做）

关键财务数据收集后，**必须**调用 `${CLAUDE_PLUGIN_ROOT}/tools/financial_rigor.py` 验算，杜绝 LLM 心算误差：

```bash
# 市值验算 = 股价 × 总股本
python3 ${CLAUDE_PLUGIN_ROOT}/tools/financial_rigor.py verify-market-cap \
  --price {价格} --shares {股本} --reported {报告市值} --currency {币种}

# 估值指标验算（PE / PB 等）
python3 ${CLAUDE_PLUGIN_ROOT}/tools/financial_rigor.py verify-valuation \
  --price {价格} --eps {EPS} --bvps {每股净资产}

# 多源交叉验证
python3 ${CLAUDE_PLUGIN_ROOT}/tools/financial_rigor.py cross-validate \
  --field {字段} --values '{JSON}' --unit {单位}

# 三情景估值（乐观 / 中性 / 悲观）
python3 ${CLAUDE_PLUGIN_ROOT}/tools/financial_rigor.py three-scenario \
  --price {价格} --eps {EPS} --shares {股本亿} \
  --growth {乐观} {中性} {悲观} --pe {乐观PE} {中性PE} {悲观PE}
```

## 报告数据点审计

长报告写完后，用 `report_audit.py` 抽样核验数据点是否真实可追溯：

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/tools/report_audit.py extract  --report {报告路径} --sample 8
python3 ${CLAUDE_PLUGIN_ROOT}/tools/report_audit.py verdict  --extracted {上一步输出}
```

## 常见坑

- **市值必须手算校验**：股价 × 总股本，再与数据站市值对比（数据站时有陈旧 / 币种错误）。
- **货币单位**：港币 / 人民币 / 美元明确标注，防混淆（尤其 ADR、A+H、双重上市）。
- **财年口径**：注意非历年制（如部分公司 3 月 / 9 月财年）。
