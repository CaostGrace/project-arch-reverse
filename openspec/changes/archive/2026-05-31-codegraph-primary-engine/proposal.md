## Why

当前 codegraph 在扫描阶段仅作为"优先选项"与手动扫描混合使用，未充分发挥其独有的结构化数据能力（节点角色 dead、manifesto 规则、复杂度门控）。同时，架构文档缺少对重构优先级和死代码的专项分析章节——这些是 codegraph 区别于手动扫描的核心价值。

## What Changes

- **BREAKING**: codegraph 模式下，所有可覆盖的分析步骤强制走 codegraph，手动扫描仅兜底 codegraph 不可覆盖的章节
- **BREAKING**: 根文档从 21 章扩展为 23 章 — 新增 §22 架构重构建议、§23 死代码与死模块
- **BREAKING**: 模块文档从 12 章扩展为 14 章 — 新增 §13 模块重构建议、§14 模块死代码
- **NEW**: 重构建议基于 codegraph manifesto/check 规则、SCC 循环依赖、复杂度门控、fan-out 分析
- **NEW**: 死代码分析基于 codegraph role=dead 节点 + 0 调用者函数

## Capabilities

### Modified Capabilities

- `architecture-doc-generation`: codegraph 模式下所有可覆盖步骤强制走 codegraph；根文档新增 §22 §23，模块文档新增 §13 §14
- `codegraph-graph-management`: 新增重构相关查询指令（manifesto 规则、dead 节点）
- `codegraph-analysis-pipeline`: Step A 追加 dead/utility/adapter 节点查询；Step C 映射表追加新章节

## Impact

- **SKILL.md**: CP1.5 明确 codegraph 模式下"所有分析走 codegraph，仅不可覆盖的走手动"；章节速查表更新
- **references/chapters-ref.md**: 根文档 21→23 章，模块文档 12→14 章，新增 §22/§23/§13(模块)/§14(模块) 定义
- **references/doc-format.md**: 新增重构建议清单格式、死代码清单格式
