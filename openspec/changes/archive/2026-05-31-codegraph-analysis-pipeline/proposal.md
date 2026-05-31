## Why

当前 CP1.5 仅完成 codegraph 的检测、安装和构建，但缺少从 graph.db 数据到架构文档的**分析转换管道**。Agent 知道"用 codegraph 替代手动扫描"，但没有具体指令说明：对每个核心能力如何生成文字说明（用途、调用者、场景）、如何从调用链推导时序图、如何汇总为模块文档各章节。这导致 codegraph 接入后实际产出与手动扫描无异——浪费了节点角色、复杂度指标、调用树等结构化数据。

## What Changes

- **NEW**: SKILL.md CP1.5 新增「codegraph 分析管道」章节 — 从 graph.db 提取数据 → 识别核心模块/能力/函数 → 生成每项的文字说明 + 流程图 + 时序图的完整指令
- **NEW**: 每个核心能力强制产出：用途说明、调用者列表、触发场景、独立时序图（≥6步）、业务流程图
- **NEW**: 关键函数档案：函数签名、复杂度、调用链、所在模块、代码位置、用途描述
- **NEW**: codegraph 数据到文档章节的完整映射表，同时查缺补漏覆盖所有文档章节

## Capabilities

### New Capabilities

- `codegraph-analysis-pipeline`: codegraph 分析管道 — 定义从 graph.db 查询到文档输出的完整转换流程，包含核心模块/能力/函数的识别、描述、图表生成

### Modified Capabilities

- `codegraph-graph-management`: 追加分析模式下的数据提取指令
- `architecture-doc-generation`: 生成阶段追加 codegraph 数据驱动的文档生成规则
- `mermaid-chart-spec`: 追加 codegraph 导出的 Mermaid 格式适配说明

## Impact

- **SKILL.md CP1.5**: 从"替代声明"扩展为"分析管道" — 含数据查询表、每项产出模板、代码示例
- **references/doc-format.md**: 新增「codegraph 核心能力档案」格式（用途+调用者+场景+时序图+流程图）
- 填补了"接入 codegraph"与"产出文档"之间的空白
