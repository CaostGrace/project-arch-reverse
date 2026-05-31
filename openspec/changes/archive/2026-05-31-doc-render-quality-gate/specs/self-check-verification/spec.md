## ADDED Requirements

### Requirement: Mermaid syntax secondary validation (Step 7)
系统 SHALL 在 CP6 自检中对所有 Mermaid 块执行二次校验。

#### Scenario: Re-scan all Mermaid blocks
- **WHEN** CP6 Step 7 执行
- **THEN** 系统 SHALL 对文档中每处 ` ```mermaid ` 代码块重新执行 10 条正则规则扫描

#### Scenario: New Mermaid issue found during self-check
- **WHEN** Step 7 发现门禁1未捕获的新语法问题（如全局引用导致的未定义节点）
- **THEN** 系统 SHALL 自动修复 → 重新自检；同问题3次失败 → 标记阻塞 → 委托 CP7

### Requirement: Document structure integrity check (Step 8)
系统 SHALL 在 CP6 自检中对文档结构、表格、链接执行全局校验。

#### Scenario: Global table column check
- **WHEN** CP6 Step 8 执行
- **THEN** 系统 SHALL 对所有文档的表格逐表校验列数一致性

#### Scenario: Global link validity check
- **WHEN** CP6 Step 8 执行
- **THEN** 系统 SHALL 提取所有 Markdown 相对链接，验证目标文件存在，输出断链清单

#### Scenario: Cross-document heading reference check
- **WHEN** 链接指向其他文档的章节（如 `auth模块架构.md#6-核心功能`）
- **THEN** 系统 SHALL 验证目标文件的对应章节标题存在

#### Scenario: Skip items from quality gates reported in final report
- **WHEN** CP6 最终报告生成
- **THEN** 系统 SHALL 汇总门禁1和门禁2的跳过项清单：位置 | 问题类型 | 跳过原因 | 建议处理方式

## MODIFIED Requirements

### Requirement: Execute comprehensive self-check with no iteration limit
系统 SHALL 执行自检循环直到全部检查项通过，检查项从 6 步扩展为 8 步。其余行为不变。

### Requirement: Generate formatted self-check report
系统 SHALL 按照标准格式输出自检报告，包含新增的 Step 7（Mermaid二次校验）和 Step 8（文档结构完整性）以及累计跳过项清单。
