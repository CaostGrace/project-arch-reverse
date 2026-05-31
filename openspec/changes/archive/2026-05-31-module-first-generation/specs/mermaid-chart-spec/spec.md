## ADDED Requirements

### Requirement: Generate cross-module sequence diagram for end-to-end features
系统 SHALL 支持生成跨模块端到端时序图，覆盖多个模块参与者的完整业务流程。

#### Scenario: Cross-module sequence diagram
- **WHEN** 某业务流程跨越多个模块（如 app→order→payment→inventory）
- **THEN** 系统 SHALL 生成端到端时序图：每个模块作为一个或多个参与者，步骤标注所属模块和代码位置

#### Scenario: Cross-module step annotation
- **WHEN** 跨模块时序图中模块 A 调用模块 B
- **THEN** 系统 SHALL 在消息箭头上标注方法名，Note 标注所属模块名和代码文件路径:行号，格式 `模块A: filepath:line`

#### Scenario: Cross-module step detail table
- **WHEN** 生成跨模块时序图
- **THEN** 系统 SHALL 在时序图下方附带步骤详解表，额外增加"所属模块"列（共 9 列：步骤、所属模块、调用者、被调用者、方法签名、参数、返回、代码位置、说明）

#### Scenario: Cross-module participant grouping
- **WHEN** 跨模块时序图包含多个模块的参与者
- **THEN** 系统 SHALL 在时序图中使用 `box` 分组语法按模块对参与者分组，如 `box "order模块" ... end`

### Requirement: Aggregate core feature index chart in root document
根文档第12章 §12.2 的核心功能索引表 SHALL 覆盖所有模块的所有核心功能。

#### Scenario: Core feature index spans all modules
- **WHEN** 生成核心功能索引表
- **THEN** 系统 SHALL 列出功能ID（F-001, F-002...）、功能名、所属模块、类型（模块/跨模块）、对应的根文档节号

#### Scenario: Index links to module document for detail
- **WHEN** 索引中某功能在根文档 §12.3 聚合展示
- **THEN** 系统 SHALL 同时标注"来源模块文档"列，指向对应模块文档 §6 的具体功能小节
