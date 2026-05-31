## Requirements

### Requirement: Detect empty chapters
系统 SHALL 检测文档中标题下无内容的空章节。

#### Scenario: Title followed by another title
- **WHEN** 两行标题之间无有效内容（如 `## 3. 项目简介` 下一行直接是 `### 3.1 子标题`）
- **THEN** 系统 SHALL 标记"⚠️ 空章节: §3 项目简介 缺少简介文本"并跳过

### Requirement: Validate heading level progression
系统 SHALL 检测标题层级跳跃。

#### Scenario: Heading level skipped
- **WHEN** `##` 标题后直接出现 `####` 标题（跳过了 `###`）
- **THEN** 系统 SHALL 将 `####` 纠正为 `###`

#### Scenario: Heading level can't be fixed
- **WHEN** 层级跳跃但纠正后可能导致语义错误
- **THEN** 系统 SHALL 标记跳过 → 输出"⚠️ 标题层级跳跃: §X 缺少中间层级"

### Requirement: Validate code block closure
系统 SHALL 检查代码块是否正确闭合。

#### Scenario: Unclosed code block
- **WHEN** 文档中存在奇数个代码块标记 ` ``` `
- **THEN** 系统 SHALL 在文档末尾补充一个 ` ``` ` 闭合标志

#### Scenario: Invalid language tag
- **WHEN** 代码块语言标识为非标准值（如 `kotln` 而非 `kotlin`）
- **THEN** 系统 SHALL 枚举合法语言标识列表，自动纠正到最接近的合法值

### Requirement: Validate internal links
系统 SHALL 验证文档中的内部链接有效性（门禁2执行，需全局文件系统扫描）。

#### Scenario: Relative link target exists
- **WHEN** Markdown 链接 `[text](relative/path.md)` 指向的文件存在
- **THEN** 系统 SHALL 标记链接有效

#### Scenario: Relative link target missing
- **WHEN** Markdown 链接指向的文件不存在
- **THEN** 系统 SHALL 标记"⚠️ 断链: [text](path.md) 目标文件不存在"，入跳过项清单

#### Scenario: Anchor link to non-existent section
- **WHEN** 链接含 `#section-name` 但文档中不存在对应标题
- **THEN** 系统 SHALL 标记"⚠️ 锚点断链: #section-name 无对应章节"

### Requirement: Validate quote block closure
系统 SHALL 检查引用块是否正确闭合。

#### Scenario: Quote block not closed
- **WHEN** `>` 引用行后有空行但后续内容应属引用块
- **THEN** 系统 SHALL 检查上下文，追加 `>` 前缀或标记跳过

### Requirement: Output structure validation report
系统 SHALL 生成文档结构校验总结。

#### Scenario: Structure check summary
- **WHEN** 结构校验完成
- **THEN** 系统 SHALL 输出：空章节 X 个（跳过）、标题跳跃 Y 个（修复/跳过）、断链 Z 个（跳过）、代码块问题 W 个（修复）
