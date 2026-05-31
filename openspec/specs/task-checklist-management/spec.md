## ADDED Requirements

### Requirement: Generate chapter-level task checklist
系统 SHALL 在生成阶段开始时，根据策略和拓扑排序创建章节级任务清单文件。

#### Scenario: Strategy A generates full task list
- **WHEN** 策略为 A（全量生成）且拓扑排序完成
- **THEN** 系统 SHALL 创建 `docs/architecture/.task-checklist.md`，包含所有模块的所有章节任务、所有核心模块的日志章节任务、根文档聚合任务

#### Scenario: Strategy B generates task list for missing modules only
- **WHEN** 策略为 B（补充生成）
- **THEN** 系统 SHALL 仅为缺失模块创建章节任务，追加根文档聚合任务

#### Scenario: Strategy C generates task list for changed modules
- **WHEN** 策略为 C（增量更新）
- **THEN** 系统 SHALL 仅为代码有变更的模块创建章节任务（标记为变更章节），追加根文档聚合任务

#### Scenario: Strategy D generates root-only task list
- **WHEN** 策略为 D（重建根文档）
- **THEN** 系统 SHALL 仅创建根文档聚合任务

#### Scenario: Task list structure
- **WHEN** 生成任务清单
- **THEN** 系统 SHALL 输出：项目元信息（名称、类型、策略、时间戳）→ 模块文档章节（按拓扑排序分层）→ 核心日志文档章节 → 根文档聚合任务

### Requirement: Track task state transitions with 5 states
系统 SHALL 支持 5 种任务状态并在任务清单中持久化。

#### Scenario: Task starts as pending
- **WHEN** 任务清单创建
- **THEN** 系统 SHALL 所有任务初始状态为 `[ ]`（未开始）

#### Scenario: Task enters in-progress state
- **WHEN** 开始执行某章节任务
- **THEN** 系统 SHALL 标记该任务为 `[>]`（进行中）并保存文件

#### Scenario: Task completes successfully
- **WHEN** 章节生成成功并写入模块文档
- **THEN** 系统 SHALL 标记该任务为 `[x]`（完成）并保存文件

#### Scenario: Task fails first time
- **WHEN** 章节生成首次失败
- **THEN** 系统 SHALL 标记该任务为 `[1]`（失败待重试）并保存文件，然后自动执行一次重试

#### Scenario: Task fails second time and gets skipped
- **WHEN** 章节生成重试后仍失败
- **THEN** 系统 SHALL 标记该任务为 `[!]`（跳过）→ 在跳过清单中记录失败原因 → 保存文件 → 继续下一任务

### Requirement: Resume from interruption
系统 SHALL 支持从任务清单的中断点恢复执行。

#### Scenario: Resume from interrupted task list
- **WHEN** CP0 检测到 `.task-checklist.md` 存在且有待处理任务
- **THEN** 系统 SHALL 将所有非终态任务（`[>]`、`[1]`）重置为 `[ ]` → 从第一个 `[ ]` 任务开始继续执行

#### Scenario: No task list exists
- **WHEN** CP0 检测到 `.task-checklist.md` 不存在
- **THEN** 系统 SHALL 走正常策略流程，在生成阶段创建新任务清单

#### Scenario: Task list is complete
- **WHEN** `.task-checklist.md` 中所有任务均为 `[x]` 或 `[!]`
- **THEN** 系统 SHALL 将清单状态标记为"已完成"，按正常流程进入 CP2

### Requirement: Execute tasks sequentially in checklist order
系统 SHALL 按任务清单顺序逐项执行任务。

#### Scenario: Sequential execution within a module
- **WHEN** 执行某模块的章节任务
- **THEN** 系统 SHALL 按 §1 → §2 → ... → §12 顺序执行，每完成一个标记 `[x]`

#### Scenario: Before moving to next task
- **WHEN** 当前任务执行完毕（成功或跳过）
- **THEN** 系统 SHALL 保存 `.task-checklist.md` 文件后再继续下一个任务

#### Scenario: Module-level completion
- **WHEN** 某模块全部 12 章任务完成
- **THEN** 系统 SHALL 更新该模块标题行的进度条为 100%，然后生成该模块的日志文档（如为核心模块）

### Requirement: Record skip reasons
系统 SHALL 在任务清单中记录 `[!]` 跳过项的失败原因。

#### Scenario: Record skip reason with context
- **WHEN** 任务标记为 `[!]`（跳过）
- **THEN** 系统 SHALL 在任务清单底部的"跳过清单"中追加条目，格式：`[!] <模块> §<编号>: <错误类型> @ <代码位置>`

#### Scenario: Skip list persists across sessions
- **WHEN** Agent 恢复执行
- **THEN** 系统 SHALL 保留已有跳过清单，新跳过项追加到末尾
