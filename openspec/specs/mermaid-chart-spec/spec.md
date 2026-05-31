## ADDED Requirements

### Requirement: Generate Mermaid dependency graph with code annotations
系统 SHALL 生成标注构建文件位置的 Mermaid flowchart 格式模块依赖图。

#### Scenario: Top-down dependency graph
- **WHEN** 模块依赖关系提取完成
- **THEN** 系统 SHALL 使用 `flowchart TD` 语法生成依赖图，节点格式 `NodeID["模块名<br>构建文件路径:行号"]`

#### Scenario: Dependency edge direction
- **WHEN** 模块 A 依赖模块 B
- **THEN** 系统 SHALL 用箭头 `A --> B` 表示依赖方向

### Requirement: Generate business flow chart with code annotations
系统 SHALL 生成标注方法和代码位置的 Mermaid flowchart 格式业务流程图。

#### Scenario: Business flow with layered subgraphs
- **WHEN** 生成业务流程图
- **THEN** 系统 SHALL 使用 `subgraph` 按架构层级（表现层/业务层/数据层）分组，每节点格式 `NodeID["步骤名称<br>ClassName.method()<br>文件路径:行号"]`

#### Scenario: Step API detail table
- **WHEN** 生成业务流程图
- **THEN** 系统 SHALL 在每个流程图下方附带步骤 API 详解表（步骤编号、步骤名称、核心方法/API、输入参数、返回结果、代码位置、说明 7 列）

#### Scenario: Key code snippet display
- **WHEN** 生成流程图中的核心步骤
- **THEN** 系统 SHALL 在每个步骤的 API 详解表下方展示对应的代码片段，标注文件路径和行号

### Requirement: Generate sequence diagram with full detail requirements
每个核心功能/能力 SHALL 有独立的时序图，满足详细度要求。

#### Scenario: Independent sequence diagram per core feature
- **WHEN** 模块包含多个核心功能/能力
- **THEN** 系统 SHALL 为每个核心功能生成独立的时序图，不允许合并为一个

#### Scenario: Minimum call steps in sequence diagram
- **WHEN** 生成核心功能时序图
- **THEN** 系统 SHALL 确保调用步骤 ≥ 6 步（不含返回箭头），覆盖完整的调用链路

#### Scenario: Minimum participants in sequence diagram
- **WHEN** 生成核心功能时序图
- **THEN** 系统 SHALL 确保参与者 ≥ 4 个（如 UI→Service→Logic→Repo→DataSource）

#### Scenario: Complete layer coverage
- **WHEN** 生成时序图
- **THEN** 系统 SHALL 覆盖表现层→业务层→数据层的完整分层，禁止 `UI->>Repo` 跳过 Service/Logic 层

#### Scenario: Return messages for all requests
- **WHEN** 时序图中出现请求消息（`->>`）
- **THEN** 系统 SHALL 确保每个请求有对应的返回消息（`-->>`），关键返回须用 Note 标注转换逻辑

#### Scenario: Code annotation in sequence diagram
- **WHEN** 生成时序图
- **THEN** 系统 SHALL 每个消息箭头标注方法名，每个参与者用 `Note right of/left of` 标注文件路径:行号

#### Scenario: Step detail table for sequence diagram
- **WHEN** 生成时序图
- **THEN** 系统 SHALL 在时序图下方附带步骤详解表（步骤、调用者、被调用者、方法签名、参数、返回、代码位置、说明 8 列）

### Requirement: Generate ER diagram for database design
系统 SHALL 生成 Mermaid erDiagram 格式的数据库 ER 图。

#### Scenario: ER diagram with entity relations
- **WHEN** 项目包含数据库
- **THEN** 系统 SHALL 使用 `erDiagram` 语法生成 ER 图，关系符号标注表间关系

#### Scenario: ER entity name without hyphens
- **WHEN** 生成 ER 图实体
- **THEN** 系统 SHALL 使用下划线或无连字符的实体名（如 `NEWS_RESOURCE`），禁止使用连字符（与关系语法 `--` 冲突）

#### Scenario: Table structure table below ER diagram
- **WHEN** 生成 ER 图
- **THEN** 系统 SHALL 在 ER 图下方附带表结构说明表，标注每个表对应的代码位置（建表 SQL/迁移文件/Entity 类路径:行号）

### Requirement: Generate class diagram with code annotations
系统 SHALL 生成标注文件路径的 Mermaid classDiagram 格式类图。

#### Scenario: Class diagram with file annotations
- **WHEN** 项目包含核心数据类
- **THEN** 系统 SHALL 使用 `classDiagram` 语法生成类图，每个类用注释 `%% 文件路径:行号` 标注代码位置

#### Scenario: Class description table
- **WHEN** 生成类图
- **THEN** 系统 SHALL 在类图下方附带类说明表（类名、文件路径、行范围、关键方法、功能说明 5 列）

### Requirement: Generate navigation flow chart
系统 SHALL 生成标注页面文件位置的 Mermaid flowchart 格式导航流程图。

#### Scenario: Left-to-right navigation flow
- **WHEN** 项目包含页面导航
- **THEN** 系统 SHALL 使用 `flowchart LR` 语法生成导航图，每个页面节点标注文件路径:行号

### Requirement: Apply Mermaid syntax safety rules
系统 SHALL 在生成所有 Mermaid 图表时应用语法安全规则。

#### Scenario: Avoid cylinder shape with parentheses
- **WHEN** 节点文本含有 `()`（如方法名）
- **THEN** 系统 SHALL 使用矩形 `["..."]` 而非圆柱体 `[(...)]`，避免括号冲突

#### Scenario: Wrap nodes with parentheses in double quotes
- **WHEN** 节点文本包含 `()`
- **THEN** 系统 SHALL 用双引号包裹整个节点文本（如 `AUTH["认证<br>AuthService.login()"]`），防止 `()` 被解析为圆角矩形语法

#### Scenario: Define all nodes before reference
- **WHEN** 箭头引用某节点 ID
- **THEN** 系统 SHALL 确保该节点 ID 已在前面定义，避免解析错误 `got 'PS'`

#### Scenario: Subgraph identifier uses valid characters
- **WHEN** 使用 subgraph 分组
- **THEN** 系统 SHALL 使用字母/数字标识符作为 subgraph ID，避免特殊字符

### Requirement: Prohibit rough or simplified sequence diagrams
系统 SHALL 禁止生成不符合详细度要求的时序图。

#### Scenario: Skipping intermediate layers
- **WHEN** 时序图出现 `UI->>Repo` 直接调用模式（跳过 Service 层）
- **THEN** 系统 SHALL 标记为错误并重新生成，补充中间层参与者和调用步骤

#### Scenario: Only 3-4 steps in sequence diagram
- **WHEN** 时序图只有 3-4 个调用步骤
- **THEN** 系统 SHALL 标记为"过于粗略"并补充到至少 6 步，沿着调用链向下追踪

#### Scenario: Merging multiple features into one diagram
- **WHEN** 一个时序图包含多个不相关的核心功能
- **THEN** 系统 SHALL 拆分为每个核心功能独立的时序图

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

### Requirement: Annotate entry trigger type in all sequence diagrams
所有时序图 SHALL 在首步标注入口的触发类型（UI/系统/外部事件）。

#### Scenario: System entry annotation format
- **WHEN** 时序图描画系统触发流程
- **THEN** 系统 SHALL 在首步使用 `Note over` 标注触发条件，格式：`Note over <参与者>: 系统触发: <条件>`

#### Scenario: External event annotation format
- **WHEN** 时序图描画外部事件流程
- **THEN** 系统 SHALL 在首步使用 `Note over` 标注事件来源，格式：`Note over <参与者>: 外部事件: <来源>`

#### Scenario: UI entry annotation format
- **WHEN** 时序图描画 UI 触发流程
- **THEN** 系统 SHALL 在首步使用 `Note over` 标注交互描述，格式：`Note over UI: UI触发: <交互描述>`

### Requirement: Auto-validate and fix Mermaid syntax errors
系统 SHALL 在生成Mermaid代码块后自动执行语法检测和修复，确保生成的图表在任何渲染器中无语法错误。

#### Scenario: Fix angle brackets in message text
- **WHEN** 时序图消息包含 `<>` 且未用双引号包裹
- **THEN** 系统 SHALL 自动用双引号包裹该消息文本

#### Scenario: Fix hash sign in message text
- **WHEN** 时序图消息包含 `#` 且未用双引号包裹
- **THEN** 系统 SHALL 自动用双引号包裹

#### Scenario: Fix empty line in loop block
- **WHEN** 时序图 loop/alt/opt 块内存在孤立空行
- **THEN** 系统 SHALL 删除块内空行

#### Scenario: Fix bare parentheses in node text
- **WHEN** 流程图节点文本含 `()` 但未用双引号包裹
- **THEN** 系统 SHALL 自动添加双引号

#### Scenario: Fix cylinder shape conflict
- **WHEN** 使用 `[( )]` 圆柱体形状且内括号导致冲突
- **THEN** 系统 SHALL 替换为 `[""]` 矩形形状

#### Scenario: Fix undefined node reference
- **WHEN** 箭头引用未定义的节点ID
- **THEN** 系统 SHALL 补充占位节点定义

#### Scenario: Fix ER entity hyphen
- **WHEN** ER图实体名包含连字符 `-`
- **THEN** 系统 SHALL 替换为下划线

#### Scenario: Fix participant special characters
- **WHEN** participant 别名含 `::` 且未用双引号包裹
- **THEN** 系统 SHALL 自动用双引号包裹

#### Scenario: Fix subgraph special characters
- **WHEN** subgraph 标识符含特殊字符
- **THEN** 系统 SHALL 替换为下划线

#### Scenario: All Mermaid blocks pass validation
- **WHEN** 所有 Mermaid 块通过 10 条规则检测
- **THEN** 系统 SHALL 输出"✅ Mermaid 语法检测通过"
