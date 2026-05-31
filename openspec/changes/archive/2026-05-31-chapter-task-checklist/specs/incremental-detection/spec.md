## ADDED Requirements

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
