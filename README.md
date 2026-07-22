# fundamental-analysis · 3C3D5M3T 基本面分析 Skill

> 一个用于 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 的 skill，把3C3D5M3T + LOGOS 基本面分析框架变成可执行流程。
>
> 输入公司代码，输出结构化的 Markdown 分析文档，含做多/做空/观望结论 + 仓位建议 + 止损位 + 跟踪计划。

---

## 这个 skill 解决什么问题

看到一家公司后，不知道该问什么问题、按什么顺序问、最后怎么得出结论。这个 skill 把原作者的方法论变成一套可执行流程：

1. **输入**：公司代码（ticker）
2. **流程**：Claude 拉客观数据 → 按 5M/3T/3D 逐项分析 → LOGOS 风险清单排查 → 得出结论
3. **输出**：结构化的 Markdown 分析文档

## 框架来源

基于《风和投资随笔1》提炼的 3C3D5M3T + LOGOS 方法论：

| 框架 | 含义 |
|------|------|
| **3C 哲学** | Cycle 周期 / Change 变化 / Certainty 确定性（思考底色） |
| **3D 驱动** | D1 ROE（价值内延）/ D2 价值外延变化 / D3 情绪估值 |
| **5M 分析** | M1 TAM / M2 份额 / M3 利润率与现金流 / M4 商业模式 / M5 管理团队 |
| **3T 时间** | T1 0-3月 / T2 3-18月 / T3 18月+ |
| **LOGOS** | 100 问风险清单 |

**核心原则**：
1. 要观测、要推测、不要预测。
2. Think Small，自下而上。
3. 投资变化（好价钱买变好的企业）。
4. 投资 + 怀疑。
5. 多中空大（做多中型企业，做空恐龙级企业）。
6. 该大的大、该小的小（单仓 ≤ 8%）。
7. 错失股票不是错，买错股票才是错。

## 安装

### 方式一：项目级安装（推荐）

把仓库克隆到你的项目 `.claude/skills/` 下：

```bash
# 在你的项目根目录
cd your-project
git clone https://github.com/yangruij1993-bit/fundamental-analysis-skill.git .claude/skills/fundamental-analysis
```

把 `commands/analyze.md` 复制到 `.claude/commands/`：

```bash
cp .claude/skills/fundamental-analysis/commands/analyze.md .claude/commands/analyze.md
```

### 方式二：用户级安装（全局）

把 skill 克隆到 `~/.claude/skills/`：

```bash
git clone https://github.com/yangruij1993-bit/fundamental-analysis-skill.git ~/.claude/skills/fundamental-analysis
cp ~/.claude/skills/fundamental-analysis/commands/analyze.md ~/.claude/commands/analyze.md
```

## 使用

在 Claude Code 中输入：

```
/analyze COST          # 美股
/analyze 600519        # A股
/analyze 0700          # 港股
/analyze NVDA 做空      # 带侧重
```

Claude 会：

1. 解析 ticker + 判断市场（美股 / A股 / H股）。
2. 并行拉客观数据（美股 WebSearch + 10-K；A股 tdx MCP；H股 HKEXnews）。
3. 按 5M 逐项分析（每个 M 标注多空偏向）。
4. 按 3T 填写时段判断（标注左侧做多主战场）。
5. 按 3D 判断股价驱动（D1 多空两义）。
6. LOGOS 风险清单排查。
7. 得出明确结论（做多 / 做空 / 观望 / 观察）。
8. 生成 Markdown 文档到指定目录。
9. 抛出待确认的主观判断给用户。

## 多空对称（核心特色）

这个框架不是单纯做多或单纯做空，而是**同一套 5M3T3D 的多空对称应用**：

| 框架 | 做多信号 | 做空信号 |
|------|---------|---------|
| **5M** | 核心：M2 份额 + M3 利润率 + M5 团队（边际变好） | 基础：M1 TAM 到顶 + M4 模式危机；周期做空看 M1/M3；结构做空看 M2/M4/M5 |
| **3T** | 主战场：T1 差 + T2 好 + 估值低（左侧） | 主战场：T1 差 + T2 差（踩烂式，顺势）；宴席式（T1 好 + T2 差 + 顶部 + catalyst） |
| **3D** | D1 高 ROE 是做多根基 | D1 高 ROE 是做空大敌 |

## 示例输出

见 [`examples/`](./examples) 目录：

- [`贵州茅台_基本面分析_20260722.md`](./examples/贵州茅台_基本面分析_20260722.md) — A 股左侧做多案例
- [`中际旭创_基本面分析_20260722.md`](./examples/中际旭创_基本面分析_20260722.md) — A 股观望案例（右侧高位 + 风险回报比 1:1）

## 依赖

- **必需**：Claude Code（Anthropic CLI）
- **可选但推荐**：
  - [tdx MCP](https://github.com/microdebug/tdx-mcp) — A 股行情 / F10 / 题材数据
  - WebSearch 工具（Claude Code 内置）

## 文件结构

```
fundamental-analysis-skill/
├── SKILL.md              # skill 定义 + 执行流程
├── framework.md          # 完整方法论（3C3D5M3T + LOGOS）
├── commands/
│   └── analyze.md        # /analyze 斜杠命令入口
├── examples/             # 示例分析文档
│   ├── 贵州茅台_基本面分析_20260722.md
│   └── 中际旭创_基本面分析_20260722.md
├── README.md
└── .gitignore
```

## 自定义

### 输出路径

默认输出路径在 `SKILL.md` 中定义。你可以根据自己的项目结构修改：

```markdown
- 美股：<project>/美股/个股/<sector>/<TICKER>/研究笔记/<TICKER>_基本面分析_<YYYYMMDD>.md
- A股：<project>/A股H股/A股/<行业>/<名称>/研究笔记/<名称>_基本面分析_<YYYYMMDD>.md
- H股：<project>/A股H股/H股/<行业>/<名称>/研究笔记/<名称>_基本面分析_<YYYYMMDD>.md
```

### 写作风格

`SKILL.md` 末尾的"写作风格"章节可以按个人偏好调整。当前默认：
- 用词克制（不用"令人震惊""史无前例""显著""强劲"）。
- 短句密集，句号结尾。
- 数据驱动，判断克制。

## 版权与引用

- 本 skill 的方法论框架基于《风和投资随笔1》提炼。
- `framework.md` 中包含少量原作者原话引用（注明出处和页码），用于说明框架逻辑，属合理使用。
- 仅供个人学习研究用途。如需商业使用，请先联系原作者。
- 本书版权归作者所有，强烈建议读者购买原书系统学习。

## License

MIT（待补充 LICENSE 文件）。默认适用 MIT 条款——可自由使用、修改、分发，作者不承担任何责任。

## 贡献

欢迎通过 issue / PR 改进：
- 补充其他行业的核心观测指标（见 `framework.md` 第八章）。
- 提供更多示例分析。
- 完善多空信号识别的边界情况。
