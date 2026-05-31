## Why

当前日志文档仅通过 grep 扫描日志语句后凭经验编写，缺少与核心能力调用链的关联。codegraph 提供了完整的 entry→core→leaf 调用链数据，但完全没有用于日志分析。日志文档与架构文档脱节——排查指南是"经验归纳"而非"代码推导"，无法回答"某日志在哪个执行路径上触发"、"哪些关键函数缺日志"、"异常传播过程中日志如何表现"等问题。

## What Changes

- **BREAKING**: 核心日志文档从 5 章扩展为 8 章
- **NEW**: §3 执行路径日志追踪 — 每条核心能力调用链上，每步函数的日志语句/缺失标注
- **NEW**: §4 错误传播与异常日志 — 核心路径上的 try-catch 链、异常类型→日志表现→传播方式
- **NEW**: §5 日志覆盖评估 — 关键路径日志覆盖率 + 缺失日志函数标注
- **MODIFIED**: §6 核心功能日志清单(原§3) 增加执行路径步骤编号列
- **MODIFIED**: §8 故障排查决策树(原§5) 从"经验归纳"改为"代码推导"——每步可能失败模式→对应日志→验证步骤

## Capabilities

### New Capabilities

- `execution-path-logging`: 执行路径日志分析 — 基于 codegraph 调用链追踪每条核心能力执行路径上的日志语句、识别日志缺失、分析异常传播与日志表现

### Modified Capabilities

- `core-log-extraction`: 日志提取从 grep 扫描扩展为调用链驱动的全路径分析
- `codegraph-analysis-pipeline`: Step A 追加调用链日志追踪指令

## Impact

- **references/chapters-ref.md**: 日志文档 5→8 章，新章节定义
- **references/log-extraction-guide.md**: 更新提取流程为调用链驱动
- **references/doc-format.md**: 新增执行路径日志追踪表格式、错误传播表格式、覆盖评估表格式、决策树格式
- **SKILL.md**: 生成阶段日志文档章节数更新
