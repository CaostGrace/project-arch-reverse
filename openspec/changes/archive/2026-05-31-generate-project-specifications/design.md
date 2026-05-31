## Context

当前 project-arch-reverse Skill 的行为定义主要通过 SKILL.md（309行）和 25 个 references/ 文件来描述。这些文件混合了流程指令、格式规范、模板和配置信息，但缺乏对每个能力的独立形式化规格。

本次设计的任务是将分散的文档信息提炼为结构化的能力规格文档，遵循 OpenSpec 的 spec-driven schema。规格文档将作为 Skill 行为的单一事实来源（Single Source of Truth），支撑后续的测试用例生成、行为验证和 Skill 质量评估。

## Goals / Non-Goals

**Goals:**
- 为 8 个核心能力域生成独立、完整的规格文档
- 每个规格包含场景（Scenarios）、验收标准（Acceptance Criteria）和边界条件
- 规格之间通过引用关系建立清晰的依赖链
- 所有规格基于现有文档（SKILL.md + references/）提取，确保一致性

**Non-Goals:**
- 不修改 SKILL.md 或任何 references/ 文件的内容
- 不改变 Skill 的现有行为逻辑
- 不创建测试代码或验证脚本（这些是后续步骤）
- 不引入新的能力或功能需求

## Decisions

### D1: 规格拆分粒度

**选择**: 按 Skill 执行管道的功能域拆分 8 个能力规格

**理由**:
- SKILL.md 定义了清晰的检查点流程（CP0→CP1→扫描→生成→CP2→CP2.5→CP3），每个检查点对应独立的功能域
- Mermaid 图表规范因为跨多个生成阶段（依赖图、流程图、时序图、类图、ER图），独立为一个能力规格更清晰
- 拆分粒度与 proposal 中定义的 8 个 capability 完全对齐

**替代方案（未采用）**:
- 按文件拆分（一个 spec 对应一个 reference 文件）→ 粒度太细，规格间耦合过高
- 单一大规格 → 粒度太粗，不便于独立测试和维护

### D2: 规格文档结构

**选择**: 采用 OpenSpec spec-driven schema 的标准结构，每个规格包含 Overview、Scenarios（GIVEN/WHEN/THEN）、Input/Output Contracts、Edge Cases

**理由**:
- 遵循团队已有的 OpenSpec 规范，与其他变更保持一致
- GIVEN/WHEN/THEN 格式适合 AI agent 的行为测试
- 边界条件章节覆盖 SKILL.md 中"异常处理"表的 13 种场景

### D3: 信息来源映射策略

**选择**: 每个规格优先从 SKILL.md 的主流程提取核心逻辑，然后逐项交叉引用 references/ 中的详细定义

**信息源优先级**:
1. SKILL.md - 流程定义、检查点逻辑、异常处理
2. references/chapters-ref.md - 文档章节结构
3. references/project-types-config.md - 类型检测规则
4. references/self-check-spec.md - 自检逻辑
5. references/mermaid-spec.md - 图表规范
6. references/doc-format.md - 格式规范
7. references/log-extraction-guide.md - 日志提取
8. references/quick-ref.md - 命令和策略决策树
9. references/*-template.md (8个) - 项目类型模板
10. references/build-guide-*.md (8个) - 编译指南

### D4: 能力依赖关系

**选择**: 建立清晰的能力依赖图（见下方），这决定了规格文档的编写顺序

```
project-detection (CP1)
    ↓
incremental-detection (CP0)
    ↓
module-dependency-analysis (扫描阶段)
    ↓
architecture-doc-generation (生成阶段) ← mermaid-chart-spec (跨阶段)
    ↓
core-log-extraction (生成阶段-日志)
    ↓
self-check-verification (CP2.5)
    ↓
human-correction (CP3)
```

## Risks / Trade-offs

| Risk | Impact | Mitigation |
|------|--------|-----------|
| 规格与 SKILL.md 不一致 | 规格失去权威性，测试不可靠 | 每个规格交叉引用 SKILL.md 原文行号；后续 SKILL.md 变更时同步更新规格 |
| references/ 文件更新导致规格过时 | 规格与实际行为脱节 | 在规格中标注信息来源路径和版本（v2.0），建立版本追踪 |
| 规格过度抽象导致难以测试 | 规格无法用于自动化验证 | 每个 Scenario 包含具体的输入输出示例，避免模糊描述 |
| 边界条件覆盖不全 | 边缘场景未定义，实现不一致 | 基于 SKILL.md 的 13 种异常处理场景逐项映射到对应能力的边界条件 |

## Open Questions

- 规格文档中是否需要包含性能指标（如"3-10分钟内完成"等 SKILL.md 中的耗时预估）？
  - 初步决定：作为非功能性需求记录在 spec 的 Additional Notes 中，不作为硬性验收标准
- 混合项目类型（Android+Flutter 等）的规格是否需要在 project-detection 之外独立定义？
  - 初步决定：作为 project-detection 的子场景处理，不独立成能力
