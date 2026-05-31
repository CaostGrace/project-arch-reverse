## 1. Mermaid 自动检测规则

- [x] 1.1 references/mermaid-spec.md: 新增"Mermaid 语法自动检测规则"章节（10 条规则 + 检测正则 + 修复方法）
- [x] 1.2 时序图规则: `<>` 尖括号、`#` 井号、loop空行、双引号冲突、Note格式
- [x] 1.3 流程图规则: 裸括号、圆柱体冲突、未定义节点、subgraph特殊字符
- [x] 1.4 ER图规则: 实体名连字符；participant特殊字符

## 2. SKILL.md 双门禁流程

- [x] 2.1 CP4: 新增"生成时质检"步骤（在写入文档后、CP5 前），包含 Mermaid 扫描 + 表格校验 + 代码块检查 + 结构检查
- [x] 2.2 CP4: 三级处理逻辑：自动修复 → 尽力修 → 跳过
- [x] 2.3 CP6: 扩展为 8 步，新增 Step 7（Mermaid二次校验）和 Step 8（文档结构完整性）
- [x] 2.4 CP6: 最终报告汇总门禁1和门禁2的全部跳过项

## 3. references/ 文档更新

- [x] 3.1 doc-format.md: 新增质检报告格式 + 跳过项清单格式
- [x] 3.2 self-check-spec.md: 自检步骤从 6 步扩展为 8 步，更新报告格式
- [x] 3.3 chapters-ref.md: 如有涉及章节号的引用，更新为 8 步自检

## 4. openspec/specs 规约同步

- [x] 4.1 mermaid-chart-spec/spec.md: 新增自动验证 requirement
- [x] 4.2 self-check-verification/spec.md: 同步 Step 7/8 + 跳过项报告

## 5. 验证

- [ ] 5.1 Mermaid 规则验证: 构造含 10 种错误的 Mermaid 块 → 确认全部检测并修复
- [ ] 5.2 表格验证: 构造列数不一致的表格 → 确认检测和尽力修复
- [ ] 5.3 链接验证: 构造断链 → 确认检测并入跳过项清单
- [ ] 5.4 端到端: 完整生成流程 → 确认双门禁均执行且输出质检报告
