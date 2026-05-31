## MODIFIED Requirements

### Requirement: Handle residual issues from CP2.5
系统 SHALL 在 CP3 阶段处理 CP2.5 自检中委托的阻塞项，处理完成后回归 CP2.5。

#### Scenario: CP2.5 delegates a blocked item
- **WHEN** CP2.5 自检标记某检查项为阻塞并委托 CP3
- **THEN** 系统 SHALL 列出阻塞项清单（检查项名称、阻塞原因、建议处理方向）→ 由用户逐项确认或修正

#### Scenario: CP3 resolves blocked item, return to CP2.5
- **WHEN** 用户在 CP3 中完成阻塞项的人工处理
- **THEN** 系统 SHALL 更新对应文档 → 自动回归 CP2.5 → 从 Step 1 开始全项重新自检

#### Scenario: User explicitly accepts residual issue
- **WHEN** 用户决定接受某阻塞项为已知残留（如"该模块为三方闭源 SDK，内部逻辑无法分析"）
- **THEN** 系统 SHALL 标记该检查项为"用户接受"→ 在 CP2.5 回归时跳过该项 → 在最终报告中注明

#### Scenario: User rejects CP3 delegation for an item
- **WHEN** 用户认为某阻塞项不应被标记为阻塞
- **THEN** 系统 SHALL 以用户判断为准，该检查项从阻塞清单中移除

### Requirement: Accept chapter-level corrections from user
系统 SHALL 在 CP3 阶段接收并按章节执行用户的人工更正。此流程同时服务于 CP3 独立运行和 CP2.5 委托回归两种场景。

#### Scenario: User corrects a specific chapter
- **WHEN** 用户提交格式为 `[章节X] 的 [内容] → [新内容]` 的更正请求
- **THEN** 系统 SHALL 定位对应章节，用新内容替换旧内容

#### Scenario: CP2.5 delegation context preserved
- **WHEN** CP3 由 CP2.5 委托触发
- **THEN** 系统 SHALL 在更正会话中展示触发委托的检查项和阻塞原因，引导用户优先处理阻塞项

#### Scenario: User requests re-generation after correction
- **WHEN** 用户完成阻塞项处理后要求重新生成某章节
- **THEN** 系统 SHALL 重新执行该章节的完整扫描和生成流程 → 提示可回归 CP2.5 自检
