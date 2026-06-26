# 安装指南

## 前置要求

- **Claude Code** ≥ 2.1（终端 `claude --version` 检查）
- **Python** ≥ 3.8（工具脚本用；`python3 --version` 检查）
- **playwright**（可选，仅 `xueqiu_scraper.py` 动态抓取需要）：
  ```bash
  pip install playwright && playwright install chromium
  ```

> 其余工具脚本（`financial_rigor.py`、`report_audit.py` 等）只用 Python 标准库，无需额外安装。

## 安装方式

### 方式 1：本地直接加载（推荐，开发/试用）

最简单，官方支持：

```bash
claude --plugin-dir /Users/bay/PycharmProjects/ai-berkshire/ai-berkshire-plugin
```

启动后即可使用 18 个 skill。

### 方式 2：本地 marketplace 安装

在 Claude Code 会话里：

```text
/plugin marketplace add /Users/bay/PycharmProjects/ai-berkshire/ai-berkshire-plugin
/plugin install ai-berkshire
```

### 方式 3：Git 分发（给别人用）

把 `ai-berkshire-plugin/` 推成独立 Git 仓库后，对方：

```text
/plugin marketplace add https://github.com/<你>/ai-berkshire-plugin.git
/plugin install ai-berkshire
```

## 触发方式（Skill 形态）

本包以 Skill 形态提供，两种触发方式都行：

- **自然语言**（Claude 按 skill 描述自主调用）：
  - 「帮我研究腾讯」
  - 「段永平会怎么看现在的拼多多」
  - 「腾讯最近股价异动，帮我归因」
- **显式调用**（等价于旧版 slash command）：
  - `/investment-research 腾讯`
  - `/dyp-ask 怎么看拼多多`

## 验证

安装后开一个**新会话**：

1. 确认 skill 已加载：在对话里说「列出你能用的 ai-berkshire 投研 skill」。
2. 冒烟测试（不依赖外部数据）：
   - 自然语言：「用段永平的思路看拼多多」
   - 或显式：`/dyp-ask 怎么看拼多多`
3. 带工具的测试（验证 `${CLAUDE_PLUGIN_ROOT}` 路径生效）：
   - `/investment-checklist 腾讯` 或「过一下腾讯的买入 checklist」—— 会调用 `financial_rigor.py` 验算

## 报告输出位置

所有 skill 把报告写到**当前工作目录**的 `reports/` 下（不是 plugin 目录）。建议在你存放研究的目录里启动：

```bash
cd ~/my-invest-research
claude --plugin-dir /path/to/ai-berkshire-plugin
/investment-research 腾讯   # 报告写到 ~/my-invest-research/reports/腾讯/
```

## 升级 / 卸载

- **升级**：更新 `ai-berkshire-plugin/` 内容后，重启 Claude Code 会话即可（plugin 在会话启动时加载）。
- **卸载**：
  - 方式 1：停止用 `--plugin-dir` 启动即可。
  - 方式 2/3：`/plugin uninstall ai-berkshire`。

## 常见问题

**Q：skill 没被触发？**
确认 plugin 已加载——方式 1 重启会话；方式 2/3 用 `/plugin list` 确认 `ai-berkshire` 存在且 enabled。若自然语言没触发，改用 `/skill-name` 显式调用。

**Q：脚本报 `FileNotFoundError`？**
`${CLAUDE_PLUGIN_ROOT}` 仅在 skill 从 plugin 加载时可用。若你把单个 SKILL.md 复制到别处，路径会失效——请用整包安装，勿拆散。

**Q：playwright 相关报错？**
仅 `xueqiu_scraper.py`（`/news-pulse` 用）需要。装好后重试；或忽略，`news-pulse` 会改用其他侦察手段。
