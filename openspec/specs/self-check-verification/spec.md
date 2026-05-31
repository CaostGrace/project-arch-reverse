## ADDED Requirements

### Requirement: Execute CP2 result confirmation
系统 SHALL 在文档生成完成后展示结果并等待用户反馈（CP2）。

#### Scenario: User confirms satisfaction
- **WHEN** 用户回复"满足需求"
- **THEN** 系统 SHALL 直接进入 CP2.5（自检验证）

#### Scenario: User requests modifications
- **WHEN** 用户提出具体修改要求（如"第7章依赖关系有误"）
- **THEN** 系统 SHALL 执行修改 → 更新文档 → 返回 CP2 重新确认

#### Scenario: User requests manual review
- **WHEN** 用户回复"需要人工审核"
- **THEN** 系统 SHALL 跳过 CP2.5 → 直接进入 CP3（人工更正）

### Requirement: Execute 3-question quick self-check first
系统 SHALL 在执行完整 5 步自检前先回答 3 个关键问题。

#### Scenario: All 3 quick questions pass
- **WHEN** 根文档 21 章齐全、每个构建文件识别的模块都有文档、所有核心模块都有日志文档
- **THEN** 系统 SHALL 确认基本覆盖完整，进入详细 5 步自检验证质量

#### Scenario: Quick check finds missing chapter
- **WHEN** 根文档某章节缺失或为空
- **THEN** 系统 SHALL 立即补充缺失章节，然后进入完整 5 步自检

#### Scenario: Quick check finds missing module doc
- **WHEN** 某构建文件识别的模块缺少模块文档
- **THEN** 系统 SHALL 立即生成缺失的模块文档，然后进入完整 5 步自检

#### Scenario: Quick check finds missing log doc
- **WHEN** 某核心模块缺少日志文档
- **THEN** 系统 SHALL 立即生成缺失的日志文档，然后进入完整 5 步自检

### Requirement: Execute 5-step comprehensive self-check loop
系统 SHALL 执行最多 3 次自检循环，每次发现问题立即处理并重新验证。

#### Scenario: First check pass
- **WHEN** 第 1 次自检未发现问题
- **THEN** 系统 SHALL 报告"验证通过"，继续下一步（CP3 人工更正或完成）

#### Scenario: First check finds issues
- **WHEN** 第 1 次自检发现问题（如章节缺失、依赖错误）
- **THEN** 系统 SHALL 立即修复问题 → 重新执行自检（进入第 2 次循环）

#### Scenario: Second check finds issues
- **WHEN** 第 2 次自检发现问题
- **THEN** 系统 SHALL 修复问题 → 重新执行自检（进入第 3 次循环）

#### Scenario: Third check still has issues
- **WHEN** 达到 3 次循环后仍有未解决问题
- **THEN** 系统 SHALL 记录残留问题到最终报告 → 继续 CP3（人工更正），不再进行第 4 次循环

### Requirement: Check root document completeness (Step 1)
系统 SHALL 逐章检查根文档 21 章的完整性和非空性。

#### Scenario: Chapter is missing
- **WHEN** 根文档某章节不存在
- **THEN** 系统 SHALL 标记缺失章节并立即生成

#### Scenario: Chapter has placeholder content
- **WHEN** 根文档某章节内容为空或仅含占位符（如"待补充"、"TODO"）
- **THEN** 系统 SHALL 标记为不完整并重新扫描填充

### Requirement: Verify dependency correctness (Step 2)
系统 SHALL 交叉验证根文档第6章依赖图与第7章依赖分析的一致性。

#### Scenario: Dependency graph edge matches text
- **WHEN** 第6章 Mermaid 图中显示 A→B 的依赖边
- **THEN** 系统 SHALL 确认第7章文本描述中包含对应的依赖关系

#### Scenario: Dependency inconsistency found
- **WHEN** 依赖图中某依赖关系在第7章中未描述（或反之）
- **THEN** 系统 SHALL 以构建文件解析结果为准进行修正，重新生成图表或文本

### Requirement: Check module document generation status (Step 3)
系统 SHALL 对比构建文件识别的模块数与已生成的模块文档数。

#### Scenario: All modules have docs
- **WHEN** 已生成模块文档数 = 构建文件识别的模块数
- **THEN** 系统 SHALL 标记模块文档状态为"全部生成"

#### Scenario: Some modules missing docs
- **WHEN** 已生成模块文档数 < 构建文件识别的模块数
- **THEN** 系统 SHALL 列出缺失模块清单 → 立即生成缺失文档

#### Scenario: Module has no source to analyze
- **WHEN** 模块无源码文件或为外部依赖
- **THEN** 系统 SHALL 记录原因（无源码/权限问题/空目录）并标注为"不可生成"

### Requirement: Check core log document completeness (Step 4)
系统 SHALL 验证核心模块日志文档的完整性和日志覆盖范围。

#### Scenario: Core module has complete log doc
- **WHEN** 核心模块的日志文档第3章覆盖了所有核心功能，每个功能 ≥3 条关键日志
- **THEN** 系统 SHALL 标记日志文档完整

#### Scenario: Core module missing log doc
- **WHEN** 核心模块缺少 `docs/logs/{模块名}/` 目录
- **THEN** 系统 SHALL 立即调用日志提取流程生成文档

#### Scenario: Log coverage incomplete
- **WHEN** 核心模块日志文档存在但某个核心功能的日志不足 3 条
- **THEN** 系统 SHALL 补充扫描遗漏日志，更新日志文档第3章

#### Scenario: Architecture change without log sync
- **WHEN** 架构文档有更新但对应日志文档未同步
- **THEN** 系统 SHALL 立即同步更新日志文档

### Requirement: Generate formatted self-check report
系统 SHALL 按照标准格式输出自检报告。

#### Scenario: Output self-check report
- **WHEN** 每次自检完成
- **THEN** 系统 SHALL 输出包含以下部分的报告：项目信息、根文档完整性、模块依赖关系验证、模块文档生成状态、核心日志文档状态、本次发现的问题

#### Scenario: Output final report after 3 cycles
- **WHEN** 3 次自检循环完成
- **THEN** 系统 SHALL 输出最终报告：已解决问题列表、残留问题列表（需人工处理）、文档生成状态汇总

### Requirement: Handle problem types during self-check
系统 SHALL 按问题类型执行对应的修复操作。

#### Scenario: Root doc chapter missing
- **WHEN** 根文档章节缺失
- **THEN** 系统 SHALL 立即生成缺失章节

#### Scenario: Mermaid chart syntax error
- **WHEN** 发现 Mermaid 图表语法错误（如节点未定义、括号冲突）
- **THEN** 系统 SHALL 重新生成正确的图表

#### Scenario: Broken link
- **WHEN** 发现文档间的链接失效
- **THEN** 系统 SHALL 修复链接

#### Scenario: Dependency relation error
- **WHEN** 发现模块依赖关系错误
- **THEN** 系统 SHALL 修正依赖关系描述，重新生成相关图表
