# Changelog

## [1.1.0] - 2026-06-26

形态升级：Command → Skill。

### 变更
- 18 个投研流程从 `commands/*.md`（legacy slash command）迁移为标准 Skill 形态 `skills/<名>/SKILL.md`
- 每个 skill 的 frontmatter 改为 `name` + 第三人称触发 `description`（决定 Claude 何时自主调用）
- 正文里的 `$ARGUMENTS` 改写为自然语言（Skill 不展开 `$ARGUMENTS`，改由 args 传入）
- 删除 `commands/` 目录，plugin 仅保留 `skills/` 形态

### 保持不变
- 触发方式：仍可 `/investment-research 腾讯` 显式调用，新增自然语言触发（说「研究腾讯」）
- 正文、方法论、大师语录、工具调用（`${CLAUDE_PLUGIN_ROOT}/tools/`）、报告输出路径

## [1.0.0] - 2026-06-26

首版可迁移 Plugin 包，从上游 ai-berkshire 项目打包而来。

### 新增
- 标准 Claude Code Plugin 结构（`.claude-plugin/plugin.json` + `marketplace.json`）
- 18 个投研 command（巴菲特 / 芒格 / 段永平 / 李录 四大师框架）
- 9 个 Python 工具脚本（financial_rigor / report_audit / xueqiu_scraper 等）
- 每个 command 增加 `description` + `argument-hint` frontmatter（正文与 `$ARGUMENTS` 保持不变）
- `references/` 方法论参考（四大师框架速查、数据源与交叉验证规范）
- `INSTALL.md` 三种安装方式 + 验证步骤

### 变更（相对上游散装 skill）
- 所有脚本路径从硬编码 `~/ai-berkshire/tools/` 改为官方可移植的 `${CLAUDE_PLUGIN_ROOT}/tools/`
- 安装方式从手动 `cp skills/*.md ~/.claude/commands/` 升级为 `claude --plugin-dir` / `/plugin install`

### 保持不变
- command 触发方式（`/名字`）与参数交互（`$ARGUMENTS`）
- command 正文、方法论、大师语录、报告输出路径（相对 `reports/`）
