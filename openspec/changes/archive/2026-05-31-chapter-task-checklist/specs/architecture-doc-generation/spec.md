## MODIFIED Requirements

### Requirement: Handle document generation by strategy
系统 SHALL 根据 CP0 确认的策略执行不同的文档生成流程。所有策略均采用模块优先（先模块文档后根文档）的生成顺序。模块文档按拓扑排序分层生成。

#### Scenario: Strategy A full generation
- **WHEN** 策略确认为 A（全量生成）
- **THEN** 系统 SHALL 创建目录结构 → **生成章节级任务清单** → 按拓扑排序和任务清单逐章节生成所有模块文档（12章/模块）→ 各模块日志文档（5章） → 生成根文档（21章，聚合模式）

#### Scenario: Strategy B supplement missing modules
- **WHEN** 策略确认为 B（补充生成）
- **THEN** 系统 SHALL 识别缺失模块 → **生成缺失模块的章节级任务清单** → 按任务清单逐章节补充生成 → 基于全部已有模块文档重新聚合生成根文档 → 同步补充缺失核心日志文档

#### Scenario: Strategy C incremental update
- **WHEN** 策略确认为 C（增量更新）
- **THEN** 系统 SHALL 对比代码 vs 文档时间戳 → **生成变更模块的章节级任务清单** → 按拓扑排序和任务清单逐章节更新 → 基于全部已有模块文档重新聚合生成根文档 → 同步更新核心日志文档

#### Scenario: Strategy D rebuild root doc
- **WHEN** 策略确认为 D（重建根文档）
- **THEN** 系统 SHALL **生成根文档聚合任务清单** → 基于已有模块文档聚合生成根文档 → 补充缺失模块文档 → 同步补充核心日志文档

#### Scenario: Resume from interrupted task list
- **WHEN** CP0 确认恢复执行
- **THEN** 系统 SHALL 读取已有 `.task-checklist.md` → 重置非终态任务 → 从第一个 `[ ]` 开始逐项执行

## ADDED Requirements

### Requirement: Execute generation with per-chapter progress tracking
系统 SHALL 在执行任务清单时逐章节追踪进度。

#### Scenario: Mark task as in-progress before execution
- **WHEN** 开始生成某模块的某章节
- **THEN** 系统 SHALL 先标记该任务为 `[>]` 并保存 `.task-checklist.md`，再开始执行

#### Scenario: Mark task as complete after successful execution
- **WHEN** 章节生成成功
- **THEN** 系统 SHALL 标记该任务为 `[x]` 并保存 `.task-checklist.md`

#### Scenario: Retry on first failure
- **WHEN** 章节生成失败
- **THEN** 系统 SHALL 标记为 `[1]` → 保存 → 立即重试一次 → 成功则标记 `[x]`，失败则标记 `[!]` 并记录原因

#### Scenario: Update module progress bar
- **WHEN** 某模块内一个章节任务完成
- **THEN** 系统 SHALL 更新该模块标题行的进度条（如 `[▰▰░░░░░░░░ 25%]`）
