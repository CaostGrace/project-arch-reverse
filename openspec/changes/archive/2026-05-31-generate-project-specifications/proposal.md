## Why

project-arch-reverse 是一个 AI agent Skill，用于自动逆向分析软件项目架构并生成完整的架构文档。当前项目有详尽的 SKILL.md 执行流程定义和 references/ 参考文档，但缺少形式化的功能规格文档（Specifications）。为提升项目的可维护性、可测试性和团队协作效率，需要为所有核心能力生成完整的功能规格文档。

## What Changes

- 为该 Skill 的所有核心能力生成规格文档（spec.md）
- 覆盖六大核心能力域：项目检测与类型识别、模块依赖分析、架构文档生成、核心日志提取、自检验证、人工更正与异常处理
- 每个能力规格包含功能描述、输入输出契约、验收标准、边界条件和异常场景
- 规格文档基于 SKILL.md 的 6 个检查点流程和 references/ 的 25 个参考文件进行提取和结构化
- 所有规格文档放置在 `openspec/specs/` 目录下，遵循 spec-driven schema

## Capabilities

### New Capabilities

- `project-detection`: 项目类型自动检测（Android/iOS/Java/Vue/React/Flutter/Node.js/HarmonyOS）、混合项目处理、用户覆盖类型
- `incremental-detection`: 增量检测策略（CP0），包括策略选择（A全量/B补充/C增量/D重建）、日志联动策略（L-A/L-B/L-C）、模块范围确认
- `module-dependency-analysis`: 模块依赖扫描、依赖矩阵生成、循环依赖检测、Mermaid 依赖图生成（含代码位置标注）
- `architecture-doc-generation`: 根文档（21章）和模块文档（12章）的生成，包括所有项目类型模板（8种）
- `core-log-extraction`: 核心模块日志文档（5章）的生成，支持多语言日志模式扫描
- `self-check-verification`: CP2.5 自检验证流程，最多3次循环，检查根文档完整性、依赖正确性、模块文档状态、日志覆盖
- `human-correction`: CP3 人工更正流程，支持按章节定向修改
- `mermaid-chart-spec`: Mermaid 图表规范（依赖图、时序图、流程图、类图、ER图、导航图），含代码位置标注规范、详细度要求

### Modified Capabilities

<!-- 无现有规格需要修改，全部为新增 -->

## Impact

- **文件系统**: 在 `openspec/specs/` 下创建 8 个能力规格目录，每个包含 `spec.md`
- **参考源**: 基于 `SKILL.md` 和 `references/` 下 25 个参考文件提取规格
- **无代码变更**: 本次仅创建文档，不修改 Skill 行为逻辑
- **后续受益**: 规格文档将作为单元测试、行为验证和 Skill 质量评估的依据
