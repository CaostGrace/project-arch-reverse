## MODIFIED Requirements

### Requirement: Track task progress with 5 status markers
系统 SHALL 使用 5 种状态标记追踪任务清单进度：`[ ]` 未开始、`[>]` 进行中、`[1]` 失败待重试、`[x]` 完成、`[!]` 跳过。

#### Scenario: [!] only for unrecoverable errors
- **WHEN** 生成过程遇到不可恢复的错误（源码文件被删除、磁盘空间不足、编码异常）
- **THEN** 系统 SHALL 标记 `[!]` 跳过并记录跳过原因

#### Scenario: [!] NOT for volume-based skipping
- **WHEN** 存在大量待生成文档
- **THEN** 系统 SHALL NOT 使用 `[!]` 跳过。系统 SHALL 继续逐项生成直到全部 `[x]`

#### Scenario: [!] NOT for user-requested partial completion
- **WHEN** 用户表示"基础够了"或"剩下的后续补充"
- **THEN** 系统 SHALL NOT 接受。系统 SHALL 继续完成全部生成

### Requirement: Resume from interrupted task checklist
系统 SHALL 在 CP0 检测到未完成任务清单时从第一个 `[ ]` 继续。

#### Scenario: Resume completes all remaining tasks
- **WHEN** 恢复执行被中断的任务清单
- **THEN** 系统 SHALL 继续生成直到所有任务为 `[x]` 或 `[!]`（不可恢复错误），不询问用户是否继续

## ADDED Requirements

### Requirement: CP4 generation iron rules
CP4 生成阶段 SHALL 遵守以下铁律，不可违反。

#### Scenario: Never ask user about generation scope
- **WHEN** 正在生成文档
- **THEN** 系统 SHALL NOT 询问"是否继续"、"是否跳过"、"是否够了"

#### Scenario: Never output "basic completion" with pending docs
- **WHEN** 存在未完成的生成任务
- **THEN** 系统 SHALL NOT 输出"基础完成"、"核心模块文档已生成，其余可补充"等暗示可停止的措辞

#### Scenario: Never suggest deferral
- **WHEN** 存在待生成的文档
- **THEN** 系统 SHALL NOT 建议"后续补充"、"下次再生成"、"分批跳过"

#### Scenario: Batch generation does not mean skip
- **WHEN** 模块数量 > 20 采用分批策略
- **THEN** 系统 SHALL 在每批完成后自动继续下一批，直至全部完成，不询问用户
