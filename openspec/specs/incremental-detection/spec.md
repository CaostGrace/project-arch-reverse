## ADDED Requirements

### Requirement: Scan existing documentation to determine strategy
系统 SHALL 在执行开始前扫描已有文档状态，自动推荐执行策略。

#### Scenario: No existing architecture docs
- **WHEN** `docs/architecture/` 目录不存在
- **THEN** 系统 SHALL 推荐策略 A（全量生成）+ L-A（全量日志生成）

#### Scenario: Root doc exists but module docs are partially missing
- **WHEN** 根文档存在但部分模块文档缺失
- **THEN** 系统 SHALL 推荐策略 B（补充生成）+ L-B（补充日志生成）

#### Scenario: All docs exist with code changes
- **WHEN** 根文档和所有模块文档均存在，且代码时间戳晚于文档
- **THEN** 系统 SHALL 推荐策略 C（增量更新）+ L-C（对比更新日志）

#### Scenario: Module docs exist without root doc
- **WHEN** 模块文档存在但根文档缺失
- **THEN** 系统 SHALL 推荐策略 D（基于模块文档重建根文档）

### Requirement: Classify modules as core or non-core
系统 SHALL 自动判定每个模块是否为核心模块，满足任一核心条件即标记为核心。

#### Scenario: Module has user-facing UI
- **WHEN** 模块包含 UI 页面或界面组件（如 Activity、ViewController、Page 组件）
- **THEN** 系统 SHALL 标记该模块为核心模块

#### Scenario: Module has background execution capability
- **WHEN** 模块包含 Service、Worker、定时任务、推送接收或后台同步逻辑
- **THEN** 系统 SHALL 标记该模块为核心模块

#### Scenario: Module has high complexity
- **WHEN** 模块包含超过 20 个文件或超过 5000 行代码
- **THEN** 系统 SHALL 标记该模块为核心模块

#### Scenario: Module is build script or pure utility
- **WHEN** 模块为构建脚本、Gradle 插件、纯工具库（无 UI 无后台且复杂度低）或测试模块
- **THEN** 系统 SHALL 标记该模块为非核心模块，不需要生成日志文档

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

### Requirement: Synchronize log document strategy with architecture document strategy
日志文档策略 SHALL 与架构文档策略联动，不可独立跳过。

#### Scenario: Architecture strategy A requires log strategy L-A
- **WHEN** 选择架构策略 A（全量生成）
- **THEN** 系统 SHALL 同时执行日志策略 L-A（为所有核心模块生成日志文档）

#### Scenario: Architecture strategy C requires log strategy L-C
- **WHEN** 选择架构策略 C（增量更新）
- **THEN** 系统 SHALL 同时执行日志策略 L-C（对比代码时间戳更新变化模块的日志文档）

#### Scenario: Architecture doc changes require log doc updates
- **WHEN** 某模块的架构文档有变更（新增或更新）
- **THEN** 系统 SHALL 同步更新该模块的核心日志文档

### Requirement: Handle incremental update by comparing timestamps
增量更新策略（C）SHALL 通过对比代码文件与文档的时间戳来决定更新范围。

#### Scenario: Module code newer than its document
- **WHEN** 模块源码文件的修改时间戳晚于对应的模块文档
- **THEN** 系统 SHALL 标记该模块为"需更新"并重新扫描生成

#### Scenario: Incremental update fails
- **WHEN** 增量更新过程中某步骤失败
- **THEN** 系统 SHALL 备份已有文档，降级为全量生成（策略 A）

### Requirement: Detect incomplete task checklist at CP0
系统 SHALL 在 CP0 增量检测阶段检测 `.task-checklist.md` 的状态。

#### Scenario: Incomplete task checklist detected
- **WHEN** `.task-checklist.md` 存在且包含 `[ ]` 或 `[1]` 状态的任务
- **THEN** 系统 SHALL 在检测报告中标注"检测到未完成任务清单"→ 显示中断位置（第一个非完成项）→ 推荐"恢复执行"模式

#### Scenario: Complete task checklist detected
- **WHEN** `.task-checklist.md` 存在且所有任务均为 `[x]` 或 `[!]`
- **THEN** 系统 SHALL 视为任务已全部处理，按正常流程继续（不推荐恢复）

#### Scenario: Task checklist absent
- **WHEN** `.task-checklist.md` 不存在
- **THEN** 系统 SHALL 走正常策略检测流程（A/B/C/D），不做特殊处理

#### Scenario: User confirms resume
- **WHEN** 检测到未完成任务清单且用户确认恢复
- **THEN** 系统 SHALL 跳转到生成阶段的"任务执行"子步骤，从第一个非完成任务开始
