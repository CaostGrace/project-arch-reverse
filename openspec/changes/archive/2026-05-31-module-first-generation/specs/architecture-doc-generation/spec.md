## MODIFIED Requirements

### Requirement: Handle document generation by strategy
系统 SHALL 根据 CP0 确认的策略执行不同的文档生成流程。所有策略均采用模块优先（先模块文档后根文档）的生成顺序。模块文档按拓扑排序分层生成。

#### Scenario: Strategy A full generation
- **WHEN** 策略确认为 A（全量生成）
- **THEN** 系统 SHALL 创建目录结构 → 按拓扑排序生成所有模块文档（12章/模块）→ 生成根文档（21章，聚合模式）→ 生成所有核心模块日志文档（5章）

#### Scenario: Strategy B supplement missing modules
- **WHEN** 策略确认为 B（补充生成）
- **THEN** 系统 SHALL 识别缺失模块 → 按拓扑排序生成缺失模块文档 → 基于全部已有模块文档重新聚合生成根文档 → 同步补充缺失核心日志文档

#### Scenario: Strategy C incremental update
- **WHEN** 策略确认为 C（增量更新）
- **THEN** 系统 SHALL 对比代码 vs 文档时间戳 → 按拓扑排序更新变化模块文档 → 基于全部已有模块文档重新聚合生成根文档 → 同步更新核心日志文档 → 确保所有核心模块日志文档存在

#### Scenario: Strategy D rebuild root doc
- **WHEN** 策略确认为 D（重建根文档）
- **THEN** 系统 SHALL 基于已有模块文档聚合生成根文档 → 补充缺失模块文档 → 同步补充核心日志文档

## ADDED Requirements

### Requirement: Root document section 12 aggregate all core features
根文档第12章 SHALL 全量汇总所有模块的核心功能，采用聚合展示模式，包含跨模块综合分析。

#### Scenario: Aggregate module-level core features
- **WHEN** 所有模块文档生成完毕
- **THEN** 系统 SHALL 在第12章 §12.1 生成跨模块整体业务概览图，§12.2 生成核心功能索引表，§12.3 按模块逐个展示核心功能（独立时序图+步骤详解表+代码片段），§12.4 分析跨模块端到端综合功能

#### Scenario: Core feature index table
- **WHEN** 聚合所有模块核心功能
- **THEN** 系统 SHALL 生成索引表列：功能ID | 功能名 | 所属模块 | 类型（模块/跨模块）| 本文档节号

#### Scenario: Cross-module end-to-end feature analysis
- **WHEN** 某业务流程跨越多个模块（如 下单=order+payment+inventory）
- **THEN** 系统 SHALL 在 §12.4 生成跨模块端到端时序图，参与者和步骤覆盖所有涉及的模块，标注每个步骤的所属模块和代码位置

### Requirement: Root document aggregates content from module documents
根文档中依赖模块文档内容的章节 SHALL 基于已生成的模块文档聚合，禁止凭空生成。

#### Scenario: Section 6 dependency graph aggregation
- **WHEN** 生成根文档第6章模块依赖关系图
- **THEN** 系统 SHALL 聚合所有模块文档第3章声明的依赖关系生成依赖图，确保依赖方向与模块文档一致

#### Scenario: Section 8 module introduction aggregation
- **WHEN** 生成根文档第8章模块介绍
- **THEN** 系统 SHALL 聚合所有模块文档第1章概述信息，模块列表与第20章索引一一对应

#### Scenario: Section 13 ER diagram aggregation
- **WHEN** 生成根文档第13章数据库设计
- **THEN** 系统 SHALL 聚合所有模块文档第7章数据依赖及实体信息生成 ER 图

#### Scenario: Section 14 class diagram aggregation
- **WHEN** 生成根文档第14章核心数据类关系图
- **THEN** 系统 SHALL 聚合所有模块文档第11章关键类信息生成类图
