## MODIFIED Requirements

### Requirement: Check module coverage and completeness (Step 1)
系统 SHALL 验证所有模块文档的覆盖率和每文档 12 章完整性。

#### Scenario: All modules have complete docs
- **WHEN** 构建文件识别 N 个模块，已生成 M 个模块文档，且每文档 12 章完整
- **THEN** 系统 SHALL 标记 Step 1 通过（M ≥ N 且每文档 12 章不缺）

#### Scenario: Module document missing
- **WHEN** 某构建文件识别的模块缺少模块文档
- **THEN** 系统 SHALL 立即生成缺失的模块文档 → 重新自检

#### Scenario: Module document has missing chapters
- **WHEN** 某模块文档章节数不足 12 或存在占位符
- **THEN** 系统 SHALL 补充缺失章节 → 重新自检

#### Scenario: Task checklist comparison
- **WHEN** `.task-checklist.md` 存在
- **THEN** 系统 SHALL 对比任务清单预期任务数 vs `[x]` 完成数 → `[!]` 跳过项列为"已知不完整" → 未执行的 `[ ]` 项列为严重缺失并立即生成
