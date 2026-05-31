## Requirements

### Requirement: Validate Mermaid sequence diagram syntax
系统 SHALL 对每个 sequenceDiagram 代码块执行自动语法检测和修复。

#### Scenario: Angle brackets in message text
- **WHEN** 时序图消息含 `<>` 尖括号（如 `List<Item>`）且未用双引号包裹
- **THEN** 系统 SHALL 自动用双引号包裹该消息文本：`->> "List<Item>"`

#### Scenario: Hash sign in message text
- **WHEN** 时序图消息含 `#` 井号且未用双引号包裹
- **THEN** 系统 SHALL 自动用双引号包裹该消息文本

#### Scenario: Empty line inside loop/alt block
- **WHEN** 时序图 `loop`/`alt`/`opt` 块内存在空行（非 `end` 行）
- **THEN** 系统 SHALL 自动删除块内空行

#### Scenario: Double quote conflict in message text
- **WHEN** 消息文本含双引号（如 `he said "hello"`）导致与外部双引号冲突
- **THEN** 系统 SHALL 将内部双引号替换为单引号：`"he said 'hello'"`

### Requirement: Validate Mermaid flowchart syntax
系统 SHALL 对每个 flowchart 代码块执行自动语法检测和修复。

#### Scenario: Bare parentheses in node text
- **WHEN** 节点文本含 `()` 但未用双引号包裹（如 `A[login()]`）
- **THEN** 系统 SHALL 自动添加双引号：`A["login()"]`

#### Scenario: Cylinder shape with parentheses conflict
- **WHEN** 节点使用圆柱体形状 `[( )]` 且内括号冲突
- **THEN** 系统 SHALL 替换为矩形：`["..."]`

#### Scenario: Undefined node reference
- **WHEN** 箭头引用了未在前文中 `NodeID[text]` 定义的节点ID
- **THEN** 系统 SHALL 在流程图顶部补充节点定义：`NodeID["占位"]` 并标记为待人工确认

#### Scenario: Special character in subgraph identifier
- **WHEN** `subgraph` 标识符含特殊字符（中文、空格、括号等）
- **THEN** 系统 SHALL 替换特殊字符为下划线或移除

### Requirement: Validate Mermaid ER diagram syntax
系统 SHALL 对每个 erDiagram 代码块执行自动语法检测和修复。

#### Scenario: Hyphen in entity name
- **WHEN** ER 图实体名含连字符 `-`（如 `news-resource`）
- **THEN** 系统 SHALL 替换为下划线：`NEWS_RESOURCE`

### Requirement: Validate Mermaid participant definition
系统 SHALL 对 participant 别名进行检测。

#### Scenario: Special characters in participant alias
- **WHEN** `participant A as Name::Class` 别名含 `::` 或其他特殊字符且未用双引号包裹
- **THEN** 系统 SHALL 自动用双引号包裹：`participant A as "Name::Class"`

### Requirement: Validate Note formatting in sequence diagram
系统 SHALL 对 Note 语句格式进行检测。

#### Scenario: Note text with improper line break
- **WHEN** `Note over A: text` 后存在不带缩进的续行文本
- **THEN** 系统 SHALL 将跨行内容用 `<br>` 合并为单行

### Requirement: Output Mermaid validation report
系统 SHALL 输出 Mermaid 块的验证结果。

#### Scenario: Mermaid validation passes all rules
- **WHEN** 所有 Mermaid 块通过 10 条规则检测
- **THEN** 系统 SHALL 输出"✅ Mermaid 语法检测通过，N 个代码块全部有效"

#### Scenario: Mermaid validation finds fixable issues
- **WHEN** Mermaid 块存在可自动修复的语法问题
- **THEN** 系统 SHALL 自动修复 → 重新扫描 → 输出"✅ 修复 X 个 Mermaid 语法问题后通过"

#### Scenario: Mermaid validation finds unfixable issues
- **WHEN** Mermaid 块存在无法自动修复的语法问题
- **THEN** 系统 SHALL 标记跳过 → 输出"⚠️ M 个 Mermaid 问题无法自动修复，已记录跳过项清单"
