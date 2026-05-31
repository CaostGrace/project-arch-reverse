## ADDED Requirements

### Requirement: Accept chapter-level corrections from user
系统 SHALL 在 CP3 阶段接收并按章节执行用户的人工更正。

#### Scenario: User corrects a specific chapter
- **WHEN** 用户提交格式为 `[章节X] 的 [内容] → [新内容]` 的更正请求
- **THEN** 系统 SHALL 定位对应章节，用新内容替换旧内容

#### Scenario: Multiple corrections in one request
- **WHEN** 用户提交多个章节的更正
- **THEN** 系统 SHALL 按顺序逐项执行所有更正

#### Scenario: User requests re-generation of a section
- **WHEN** 用户要求重新生成某章节（如"重新扫描第6章模块依赖"）
- **THEN** 系统 SHALL 重新执行对应章节的扫描和生成流程

### Requirement: Support iterative correction cycle
系统 SHALL 支持多次迭代更正直到用户满意。

#### Scenario: User requests additional corrections
- **WHEN** 一轮更正完成后用户提出新的更正需求
- **THEN** 系统 SHALL 执行新更正 → 展示更新后文档 → 等待用户确认

#### Scenario: User confirms final version
- **WHEN** 用户表示"确认完成"或"不再需要更正"
- **THEN** 系统 SHALL 结束 CP3，标记文档为最终版本

### Requirement: Handle residual issues from CP2.5
系统 SHALL 在 CP3 阶段处理 CP2.5 自检中记录的残留问题。

#### Scenario: Residual dependency issue
- **WHEN** CP2.5 最终报告记录了"模块 X 依赖关系需要人工确认"
- **THEN** 系统 SHALL 在 CP3 开始时优先展示残留问题列表，引导用户逐一确认或修正

#### Scenario: Residual missing data issue
- **WHEN** CP2.5 残留问题为"数据库设计部分信息缺失"
- **THEN** 系统 SHALL 提示用户提供缺失信息或确认跳过

### Requirement: Preserve document revision history
系统 SHALL 在每次 CP3 更正后更新文档修订历史。

#### Scenario: Update revision history after correction
- **WHEN** CP3 更正执行完成
- **THEN** 系统 SHALL 在根文档第1章（文档修订历史）中追加本次更正的记录，包含修订内容和日期

### Requirement: Exit CP3 gracefully
系统 SHALL 在用户确认完成后正常退出 CP3。

#### Scenario: User decides to skip remaining corrections
- **WHEN** 用户选择跳过某残留问题的修复
- **THEN** 系统 SHALL 在最终报告中标明该问题为"用户选择跳过"，不强制修复
