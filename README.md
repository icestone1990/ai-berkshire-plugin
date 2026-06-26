# ai-berkshire · 价值投资研究 Plugin

基于 Claude Code 的价值投研 Skill 合集。以 **巴菲特、芒格、段永平、李录** 四大师方法论为骨架，覆盖从选股筛选、深度研究、财报精读、组合管理到供应链瓶颈套利的完整投研工作流。

> 装包即用：`claude --plugin-dir ./ai-berkshire-plugin`，然后说「研究腾讯」或 `/investment-research 腾讯`。
> 安装细节见 [INSTALL.md](./INSTALL.md)。

## 为什么是 Plugin + Skill

原项目里 18 个投研流程是散装的 `.md` 文件，靠手动复制安装，脚本路径硬编码——换机器就坏。本包打成标准 Claude Code Plugin，并以官方推荐的 **Skill 形态**（`skills/<名>/SKILL.md`）提供：

- **自包含**：18 个 skill + 9 个工具脚本都在包内
- **可移植**：脚本路径走官方 `${CLAUDE_PLUGIN_ROOT}`，装在哪都能跑
- **双重触发**：既可自然语言触发（说「研究腾讯」，Claude 按 skill 描述自主调用），也可 `/skill-name` 显式调用
- **一键安装**：`claude --plugin-dir` 或 `/plugin install`

## 18 个 Skill（按投研流程分组）

### 选股 · 筛选漏斗
| Skill | 用途 |
|---|---|
| `/quality-screen [公司/行业/指数/主题]` | 7 条指标快速去劣，排除非一流公司 |
| `/industry-funnel [行业]` | 行业漏斗：从全市场逐层精选到 3 家 |
| `/industry-research [行业]` | 产业链全景扫描 + 四大师个股框架 |
| `/investment-checklist [公司]` | 巴菲特买入前 Checklist |

### 深度 · 公司研究
| Skill | 用途 |
|---|---|
| `/investment-research [公司]` | 四大师综合分析框架（单 Agent） |
| `/investment-team [公司]` | 四角色并行研究（Team 多 Agent） |
| `/management-deep-dive [公司/人]` | 管理层纵深：买股票就是买人 |
| `/private-company-research [公司]` | 未上市公司多 Agent 深度研究 |
| `/deep-company-series [公司]` | 8 篇长文拆一家公司（公众号系列） |

### 跟踪 · 财报与持仓
| Skill | 用途 |
|---|---|
| `/earnings-review [公司 季度]` | 财报精读：一手资料解读（单 Agent） |
| `/earnings-team [公司 季度]` | 四大师并行解读财报 + 公众号发布 |
| `/thesis-tracker [公司]` | 投资论文追踪：买入后的纪律系统 |
| `/portfolio-review [持仓]` | 组合管理：从研究公司到管理组合 |
| `/news-pulse [公司]` | 股价异动快速归因（4 Agent 情报） |

### 套利 · 主题与内容
| Skill | 用途 |
|---|---|
| `/bottleneck-hunter [超级趋势]` | 供应链瓶颈猎手：产业链咽喉套利 |
| `/wechat-article [主题]` | 公众号文章：作者-编辑-读者三 Agent |
| `/dyp-ask [问题]` | 段永平人设问答 |
| `/financial-data` | 财务数据获取与交叉验证规范 |

> 表中 `/xxx` 为显式调用；同样可用自然语言触发，如「帮我研究腾讯」「财报怎么看」「组合帮我看看」。

## 典型工作流

```
/quality-screen 行业            → 去劣
  ↓
/industry-funnel 行业           → 精选 3 家
  ↓
/investment-team 公司           → 四大师深度研究
  ↓
/investment-checklist 公司      → 买入前过关
  ↓ （买入后）
/thesis-tracker 公司            → 论文追踪
/earnings-team 公司 季度        → 每季财报复核
/news-pulse 公司                → 异动归因
/portfolio-review 持仓          → 组合再平衡
```

## 目录结构

```
ai-berkshire-plugin/
├── .claude-plugin/     plugin.json + marketplace.json
├── skills/             18 个投研 skill（每个一个目录 + SKILL.md）
├── tools/              9 个 Python 工具（财务验算 / 报告审计 / 雪球抓取…）
├── references/         方法论参考（四大师框架 / 数据源规范）
├── INSTALL.md          安装指南
└── LICENSE             MIT
```

## 核心原则

- **客观、客观、客观**：基于事实和数据，严禁主观臆断
- 严格区分"事实"与"观点"，每个判断附正反两面论据
- 金融数据用 `tools/financial_rigor.py` 精确计算（Decimal），多源交叉验证
- 强制给结论：通过 / 不通过 / 灰色，带价格区间，不打太极

详见 [references/](./references/)。

## 关于 Skill 形态

本包采用官方推荐的 Skill 形态（`skills/<名>/SKILL.md`）。每个 skill 的 frontmatter `description` 写明了触发场景（第三人称 + 触发短语），Claude 据此自主调用。两种触发方式：

- **自然语言**：「帮我研究腾讯」「段永平会怎么看拼多多」「组合帮我再平衡一下」
- **显式调用**：`/investment-research 腾讯`

每个 SKILL.md 正文保留了原投研流程的完整内容（四大师框架、反偏见机制、工具调用步骤）。后续可对偏大的 skill（如 `private-company-research`、`bottleneck-hunter`）做渐进式拆分：精简 SKILL.md 核心 + 把详细内容移到 `references/`，以符合官方 progressive disclosure 建议。

## 上游项目

完整研究报告、实盘记录、长 README 见上游仓库：<https://github.com/xbtlin/ai-berkshire>

License: MIT
