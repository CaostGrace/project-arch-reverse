## ADDED Requirements

### Requirement: Validate table column count consistency
系统 SHALL 对生成的每个 Markdown 表格执行列数一致性校验。

#### Scenario: Table has consistent columns
- **WHEN** 表头行、分隔行、所有数据行的管道符 `|` 分割列数一致
- **THEN** 系统 SHALL 标记该表格校验通过

#### Scenario: Table has inconsistent columns
- **WHEN** 数据行管道符数量与表头不一致
- **THEN** 系统 SHALL 标记该表格为列数不一致，尝试按表头列数矫正数据行（截断多余 / 追加空列）

#### Scenario: Pipe in cell content causes split
- **WHEN** 表格单元内容含 `|` 管道符（如 `"msg | detail"`）
- **THEN** 系统 SHALL 自动转义为 `\|`

#### Scenario: Table fix attempt succeeds
- **WHEN** 尽力修复后表格列数一致
- **THEN** 系统 SHALL 输出"✅ 表格 XYZ 列数不一致已修复（截断1列）"

#### Scenario: Table fix attempt fails
- **WHEN** 尽力修复后表格列数仍不一致（如结构化数据源缺失）
- **THEN** 系统 SHALL 标记跳过 → 输出"⚠️ 表格 XYZ 列数不一致无法自动修复，建议人工检查"

### Requirement: Output table validation report
系统 SHALL 汇总所有表格的校验结果。

#### Scenario: All tables pass
- **WHEN** 所有表格列数一致
- **THEN** 系统 SHALL 输出"✅ 表格校验通过，N 个表格全部一致"

#### Scenario: Some tables have issues
- **WHEN** 部分表格存在列数问题
- **THEN** 系统 SHALL 输出报告：修复 X 个 / 跳过 Y 个，含具体表格位置
