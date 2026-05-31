## MODIFIED Requirements

### Requirement: Classify modules as core or non-core
系统 SHALL 自动判定每个模块是否为核心模块，满足任一核心条件即标记为核心。

#### Scenario: Module has user-facing UI
- **WHEN** 模块包含 UI 页面或界面组件（如 Activity、ViewController、Page 组件）
- **THEN** 系统 SHALL 标记该模块为核心模块

#### Scenario: Module has background execution capability
- **WHEN** 模块包含 Service、Worker、定时任务、推送接收或后台同步逻辑
- **THEN** 系统 SHALL 标记该模块为核心模块

#### Scenario: Module has high complexity
- **WHEN** 模块包含超过 10 个文件或超过 2000 行代码
- **THEN** 系统 SHALL 标记该模块为核心模块

#### Scenario: Module is build script or pure utility
- **WHEN** 模块为构建脚本、Gradle 插件、纯工具库（无 UI 无后台且复杂度低）或测试模块
- **THEN** 系统 SHALL 标记该模块为非核心模块

### Requirement: Present detection report and wait for user confirmation
系统 SHALL 在 CP0 阶段输出检测报告并等待用户确认，不可跳过。

#### Scenario: User confirms recommended strategy
- **WHEN** 展示检测报告后用户确认推荐策略
- **THEN** 系统 SHALL 按照推荐策略继续执行 CP1

#### Scenario: User adjusts module scope
- **WHEN** 用户要求增删模块、排除/包含特定模块
- **THEN** 系统 SHALL 以用户指定的模块范围为准，覆盖自动检测结果

#### Scenario: User overrides core module marking
- **WHEN** 用户要求添加或移除某个模块的核心标记
- **THEN** 系统 SHALL 以用户指定的核心/非核心标记为准

#### Scenario: User overrides strategy
- **WHEN** 用户要求强制使用特定策略（如要求全量 A 而非增量 C）
- **THEN** 系统 SHALL 使用用户指定的策略，忽略推荐策略

#### Scenario: Detection report shows log trigger module list
- **WHEN** CP0 检测报告输出（codegraph 可用时）
- **THEN** 系统 SHALL 在报告中展示"日志触发模块列表"（= 核心模块 ∪ codegraph 高复杂度函数所在模块 ∪ codegraph 关键调用链模块），并区分标注各模块的触发原因

#### Scenario: Detection report shows log trigger list in manual mode
- **WHEN** CP0 检测报告输出（codegraph 不可用时）
- **THEN** 系统 SHALL 在报告中仅展示核心模块作为日志触发列表，并标注"[手动模式] 函数级复杂度无法精确判定，日志触发范围可能不完整"

### Requirement: Synchronize log document strategy with architecture document strategy
日志文档策略 SHALL 与架构文档策略联动，不可独立跳过。

#### Scenario: Architecture strategy A requires log strategy L-A
- **WHEN** 选择架构策略 A（全量生成）
- **THEN** 系统 SHALL 同时执行日志策略 L-A（为日志触发模块列表中的所有模块生成日志文档）

#### Scenario: Architecture strategy C requires log strategy L-C
- **WHEN** 选择架构策略 C（增量更新）
- **THEN** 系统 SHALL 同时执行日志策略 L-C（对比代码时间戳更新日志触发模块列表中变化模块的日志文档）

#### Scenario: Architecture doc changes require log doc updates
- **WHEN** 某模块的架构文档有变更（新增或更新）
- **THEN** 系统 SHALL 同步更新该模块及其关联的第二层日志文档（函数级 + 场景级）

## ADDED Requirements

### Requirement: Determine log trigger module list independently from core module classification
系统 SHALL 在 CP0 阶段独立构建"日志触发模块列表"，不仅包含核心模块，还包含 codegraph 检测到的高复杂度函数所在模块和关键调用链涉及模块。

#### Scenario: Log trigger list includes codegraph-highlighted modules
- **WHEN** CP2 codegraph 分析完成后产生日志触发标记数据
- **THEN** 系统 SHALL 将日志触发标记数据合并到 CP0 的日志触发模块列表中

#### Scenario: Log trigger list union of all sources
- **WHEN** 日志触发模块列表构建
- **THEN** 系统 SHALL 取并集：日志触发模块 = 核心模块列表 ∪ codegraph 高复杂度函数所在模块列表 ∪ codegraph 关键调用链模块列表

#### Scenario: User can adjust log trigger list
- **WHEN** 用户要求添加或移除某个模块的日志触发标记
- **THEN** 系统 SHALL 以用户指定的日志触发范围为准

### Requirement: Expanded CP0 detection report
CP0 检测报告 SHALL 包含日志相关扩展信息。

#### Scenario: Detection report includes two-layer log plan
- **WHEN** 检测报告输出
- **THEN** 系统 SHALL 展示：① 日志触发模块列表（含触发原因标注）、② 预计生成的第一层日志数量（模块级）、③ 预计生成的第二层日志数量（函数级 + 场景级，codegraph 可用时）、④ 无日志模块列表及原因
