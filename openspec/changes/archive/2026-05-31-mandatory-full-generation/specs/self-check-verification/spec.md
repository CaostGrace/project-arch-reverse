## MODIFIED Requirements

### Requirement: Execute comprehensive self-check with no iteration limit
系统 SHALL 执行自检循环直到全部检查项通过，对可自动修复的问题和不可自动修复的问题进行区分。

#### Scenario: Auto-fixable issues MUST be fixed
- **WHEN** 自检发现模块缺失、日志文档缺失、章节缺失、依赖不一致等可自动修复问题
- **THEN** 系统 SHALL 立即修复。此类问题不可通过"用户接受残留"退出。同问题3次失败 → 标记阻塞 → 委托 CP7

#### Scenario: Non-auto-fixable issues can be delegated
- **WHEN** 自检发现架构判断需领域知识、非标准结构无法解析等问题
- **THEN** 系统 SHALL 标记阻塞 → 委托 CP7

#### Scenario: User accept residue only for CP7 delegated items
- **WHEN** CP7 处理完成后仍有无法修复的阻塞项
- **THEN** 系统 SHALL 允许用户在 CP6 最终报告中选择接受该阻塞项为残留

#### Scenario: User accept NOT for document generation issues
- **WHEN** 自检发现文档缺失（模块缺失、日志缺失、章节缺失）
- **THEN** 系统 SHALL NOT 允许用户以"接受残留"退出。此类问题必须修复。

### Requirement: Handle problem types during self-check
系统 SHALL 按问题类型执行对应的修复操作。

#### Scenario: Document missing is auto-fixable
- **WHEN** 自检发现模块文档、日志文档或章节缺失
- **THEN** 系统 SHALL 立即生成，不询问用户。不计入"用户可接受残留"范畴。

## ADDED Requirements

### Requirement: Prohibited behaviors during generation and self-check
系统 SHALL 在生成和自检阶段遵守禁止行为清单。

#### Scenario: Prohibit asking user about completeness
- **WHEN** 生成或自检阶段发现待完成的任务
- **THEN** 系统 SHALL NOT 询问"是否继续"、"是否跳过分批"、"基础功能已覆盖，是否停止"

#### Scenario: Prohibit partial completion labeling
- **WHEN** 部分文档已生成但仍有未生成文档
- **THEN** 系统 SHALL NOT 输出"基础完成"、"核心完成"、"主要模块已完成"等暗示可结束的标注
