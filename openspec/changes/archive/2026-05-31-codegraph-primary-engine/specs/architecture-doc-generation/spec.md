## MODIFIED Requirements

### Requirement: Handle document generation by strategy
系统 SHALL 根据 CP0 确认的策略执行不同的文档生成流程。codegraph 模式下所有可覆盖步骤 SHALL 强制走 codegraph。模块文档按拓扑排序分层生成。

#### Scenario: Strategy A full generation with codegraph
- **WHEN** 策略确认为 A（全量生成）且 codegraph 可用
- **THEN** 系统 SHALL 创建目录结构 → 生成章节级任务清单 → codegraph 执行所有分析 → 生成根文档（23章，codegraph 模式）→ 生成所有模块文档（14章，codegraph 模式）

#### Scenario: Strategy A full generation without codegraph
- **WHEN** 策略确认为 A（全量生成）且 codegraph 不可用
- **THEN** 系统 SHALL 执行完整 8 步手动扫描 → 生成根文档（21章）→ 生成所有模块文档（12章）→ 生成核心日志文档

## ADDED Requirements

### Requirement: Generate architecture refactoring suggestions (Root §22)
系统 SHALL 在 codegraph 模式下生成根文档 §22 架构重构建议。

#### Scenario: Generate manifesto violation list
- **WHEN** codegraph 可用且 manifesto 规则已配置或使用默认规则
- **THEN** 系统 SHALL 查询 codegraph check 输出，列出边界违规、复杂度超标、循环依赖等问题的优先级排序清单

#### Scenario: Generate complexity hotspot list
- **WHEN** codegraph 可用
- **THEN** 系统 SHALL 列出 cognitive complexity > 15 的函数，标注文件:行号和建议操作（拆分/重构）

#### Scenario: Generate coupling analysis
- **WHEN** codegraph 可用
- **THEN** 系统 SHALL 列出 fan-out > 10 的高耦合函数，标注被调用者列表，建议解耦方案

#### Scenario: No significant issues found
- **WHEN** codegraph 检查未发现显著问题
- **THEN** 系统 SHALL 标注"未发现显著架构问题"而非省略章节

### Requirement: Generate dead code analysis (Root §23)
系统 SHALL 在 codegraph 模式下生成根文档 §23 死代码与死模块分析。

#### Scenario: List dead functions
- **WHEN** codegraph 可用且存在 role=dead 节点
- **THEN** 系统 SHALL 列出所有死函数：函数名 | 文件:行号 | 复杂度 | 最后修改

#### Scenario: List zero-caller functions
- **WHEN** codegraph 可用且存在 0 调用者函数
- **THEN** 系统 SHALL 列出孤立函数清单，标注可能原因（入口函数 / 废弃 / 未接入）

#### Scenario: Identify dead modules
- **WHEN** codegraph 可用且某模块所有节点均为 dead 或 utility
- **THEN** 系统 SHALL 标注为"死模块"并给出删除/归档建议

#### Scenario: No dead code found
- **WHEN** codegraph 未发现 dead 节点
- **THEN** 系统 SHALL 标注"未发现死代码"而非省略章节

### Requirement: Generate module-level refactoring suggestions (Module §13)
系统 SHALL 在 codegraph 模式下为每个模块生成 §13 模块重构建议。

#### Scenario: Module-level complexity hotspots
- **WHEN** codegraph 可用且模块内存在 cognitive > 15 函数
- **THEN** 系统 SHALL 列出模块内高复杂度函数清单，标注建议操作

#### Scenario: Module-level coupling issues
- **WHEN** codegraph 可用且模块内存在高 fan-out 函数
- **THEN** 系统 SHALL 列出高耦合函数及外部依赖

### Requirement: Generate module-level dead code list (Module §14)
系统 SHALL 在 codegraph 模式下为每个模块生成 §14 模块死代码清单。

#### Scenario: Module dead functions
- **WHEN** codegraph 可用且模块内存在 role=dead 节点
- **THEN** 系统 SHALL 列出模块内所有死函数：函数名 | 文件:行号 | 说明

#### Scenario: Module clean
- **WHEN** 模块内无 dead 节点
- **THEN** 系统 SHALL 标注"模块无死代码"
