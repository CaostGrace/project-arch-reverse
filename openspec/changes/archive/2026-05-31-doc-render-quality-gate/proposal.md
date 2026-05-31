## Why

当前生成的 Markdown 文档存在系统性渲染错误风险：Mermaid 图表中的尖括号 `<>` 和井号 `#` 会破坏解析、表格列数不一致导致错位、内部链接指向不存在的文件造成 404、代码块未闭合吞噬后续内容。现有防御完全依赖人工目检（"Mermaid图表语法正确？"单条 check），没有自动化预检机制。生成的架构文档是交付产物，渲染错误直接导致用户对 skill 的不信任。

## What Changes

- **新增** 双门禁质检机制：门禁1（CP4生成时立即检）+ 门禁2（CP6自检时全量验）
- **新增** Mermaid 10 条语法规则自动检测 + 自动修复（`<>` `#` 裸括号 空行 未定义节点 连字符 双引号冲突 Note格式 cylindrical等）
- **新增** 表格列数一致性校验（表头/分隔行/数据行列数交叉比对）
- **新增** 内部链接有效性验证（提取所有相对链接，验证目标文件存在）
- **新增** 代码块质量检查（语言标识合法性、闭合配对）
- **新增** 文档结构检查（空章节检测、标题层级跳跃、引用块闭合）
- **新增** 跳过项报告机制（不可自动修复的渲染问题标记跳过，在 CP5 确认和 CP6 最终报告中体现）
- **修改** CP4 新增"生成时质检"步骤（在写入文档后、进入 CP5 前）
- **修改** CP6 自检从 6 步扩展为 8 步（Step 7 Mermaid二次校验 + Step 8 文档结构完整性）

## Capabilities

### New Capabilities

- `mermaid-syntax-auto-check`: 对生成的每条 Mermaid 代码块执行 10 条正则规则的自动语法检测和修复（尖括号、井号、裸括号、空行、未定义节点、连字符、双引号冲突、Note格式、圆柱体、subgraph特殊字符）
- `table-column-validation`: 对每个 Markdown 表格逐表校验表头行、分隔行、数据行的列数一致性，自动检测管道符冲突并尽力修复
- `doc-structure-validation`: 对生成的文档执行结构完整性检查：空章节、标题层级跳跃、代码块闭合、内部链接有效性

### Modified Capabilities

- `mermaid-chart-spec`: 新增自动验证规则章节
- `self-check-verification`: 自检从 6 步扩展为 8 步
- `doc-format`: 新增质检报告格式和跳过项清单格式

## Impact

- SKILL.md: CP4 新增生成时质检步骤 + CP6 扩展为 8 步
- references/mermaid-spec.md: 新增"Mermaid 语法自动检测规则"章节（10 条规则 + 检测正则 + 修复方法）
- references/doc-format.md: 新增质检报告格式 + 跳过项清单格式
- references/self-check-spec.md: 自检步骤扩展
- openspec/specs/self-check-verification/spec.md: Step 7/8 新增
- openspec/specs/mermaid-chart-spec/spec.md: 自动验证 requirement
