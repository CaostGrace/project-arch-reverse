## ADDED Requirements

### Requirement: Query codegraph for core nodes per module
系统 SHALL 从 codegraph graph.db 查询每个模块的 core/entry 节点，作为核心能力和关键函数的候选。

#### Scenario: Fetch core nodes
- **WHEN** codegraph 分析模式激活
- **THEN** 系统 SHALL 查询所有 role=core 的节点，按所属模块分组

#### Scenario: Fetch entry nodes
- **WHEN** codegraph 分析模式激活
- **THEN** 系统 SHALL 查询所有 role=entry 的节点，作为核心功能入口

#### Scenario: Fall back to CLI if MCP unavailable
- **WHEN** codegraph MCP server 不可用
- **THEN** 系统 SHALL 使用 CLI 命令（`codegraph audit`、`codegraph context`）获取节点数据

### Requirement: Generate core capability profile for each core node
系统 SHALL 对每个 role=core 的节点生成完整核心能力档案。

#### Scenario: Generate capability profile
- **WHEN** core 节点识别完成
- **THEN** 系统 SHALL 为每个节点产出：用途说明、调用者列表（含触发类型）、被调用者列表、当前场景、独立时序图（≥6步）、业务流程图

#### Scenario: Infer purpose from context
- **WHEN** 生成用途说明
- **THEN** 系统 SHALL 基于函数名语义 + 上下文代码片段推断功能描述，标注"需人工确认"以提示用户校验

#### Scenario: Identify callers with trigger types
- **WHEN** 生成调用者列表
- **THEN** 系统 SHALL 使用 `codegraph impact` 查询调用方，按入口节点角色分类为 UI触发/系统触发/外部事件

#### Scenario: Deduplicate profiles
- **WHEN** 同一核心功能在 codegraph 中有多个 core 节点
- **THEN** 系统 SHALL 合并为一份档案，列出所有相关节点

### Requirement: Generate key function documentation
系统 SHALL 按复杂度和调用次数排序，提取关键函数并生成文档。

#### Scenario: Rank functions by complexity and call count
- **WHEN** 生成模块文档第11章
- **THEN** 系统 SHALL 按认知复杂度 + 被调用次数排序，取 top N 作为关键类和方法

#### Scenario: Generate function detail panel
- **WHEN** 记录关键函数
- **THEN** 系统 SHALL 输出属性面板：功能、完整签名、参数、返回、调用链（代码位置）、代码示例

### Requirement: Map codegraph data to all applicable document chapters
系统 SHALL 将 codegraph 数据映射到每个适用的文档章节，不可覆盖的章节标注"降级手动"。

#### Scenario: Map to root document chapters
- **WHEN** codegraph 分析模式生成根文档
- **THEN** 系统 SHALL 用 codegraph 数据填充 §6(依赖图)、§7(依赖分析)、§8(模块介绍)、§12(业务流程)、§13(ER图)、§14(类图)；§10/11/15-19 保持手动

#### Scenario: Map to module document chapters
- **WHEN** codegraph 分析模式生成模块文档
- **THEN** 系统 SHALL 用 codegraph 数据填充 §1(概述)、§4(业务流程)、§5(数据流)、§6(核心功能流程图)、§11(关键类方法)；§2/9/10/12 保持手动

### Requirement: Derive sequence diagrams from call chains
系统 SHALL 从 codegraph 调用链自动推导时序图。

#### Scenario: Generate sequence diagram for core capability
- **WHEN** core 节点调用链查询完成
- **THEN** 系统 SHALL 从 entry → core → leaf 绘制完整时序图，≥6步，≥4参与者，标注方法名+代码位置

#### Scenario: Reverse trace call chain
- **WHEN** 需要从 core 节点向上追溯入口
- **THEN** 系统 SHALL 使用 `codegraph impact` 查询反向依赖，找到所有 entry 节点作为时序图起点
