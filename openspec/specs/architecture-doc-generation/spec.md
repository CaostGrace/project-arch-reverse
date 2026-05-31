## ADDED Requirements

### Requirement: Generate root architecture document with 21 chapters
系统 SHALL 根据项目类型模板生成包含全部 21 章的根架构文档。

#### Scenario: Generate root doc with all chapters
- **WHEN** 执行策略 A（全量生成）或需要创建根文档
- **THEN** 系统 SHALL 创建 `docs/architecture/{项目名}项目架构文档.md`，包含全部 21 章

#### Scenario: Missing data for optional chapter
- **WHEN** 某可选章节无对应数据（如无数据库则无第13章、无页面导航则无第10-11章）
- **THEN** 系统 SHALL 标注"无"而非省略该章节

#### Scenario: Minimal viable document
- **WHEN** 项目简单或时间紧急
- **THEN** 系统 SHALL 至少完成 P0（核心）+ P1（必须）+ P2（补充）章节，P3 章节可简要标注

### Requirement: Generate module architecture documents for ALL modules
系统 SHALL 为每个模块生成包含 12 章的模块架构文档，不可省略任何模块。

#### Scenario: All modules must have architecture docs
- **WHEN** 生成阶段执行
- **THEN** 系统 SHALL 为构建文件识别出的每个模块生成 `docs/architecture/modules/{模块名}/{模块名}模块架构.md`

#### Scenario: Module appears simple
- **WHEN** 某个模块文件数少、功能简单
- **THEN** 系统 SHALL 仍然生成完整的模块架构文档（不允许跳过）

#### Scenario: Module document generation fails
- **WHEN** 某模块文档生成过程中出错
- **THEN** 系统 SHALL 记录该模块和错误原因，继续生成下一个模块，最终报告中列出失败模块

### Requirement: Apply type-specific templates
系统 SHALL 根据 CP1 确认的项目类型加载对应的模板文件。

#### Scenario: Android project uses Android template
- **WHEN** 项目类型确认为 Android
- **THEN** 系统 SHALL 使用 `references/android-template.md` 生成根文档和模块文档

#### Scenario: Mixed project uses multiple templates
- **WHEN** 项目为混合类型（如 Android + Flutter 插件）
- **THEN** 系统 SHALL 根文档使用主类型模板，子模块使用各自类型模板

#### Scenario: User-specified type overrides template
- **WHEN** 用户手动指定项目类型
- **THEN** 系统 SHALL 使用用户指定类型对应的模板，忽略自动检测的模板

### Requirement: Annotate code locations in all charts
所有图表中涉及代码的节点/参与者 SHALL 标注方法名和代码文件路径:行号。

#### Scenario: Dependency graph code annotation
- **WHEN** 生成第6章模块依赖关系图
- **THEN** 系统 SHALL 在每个模块节点标注构建文件路径:行号

#### Scenario: Business flow chart code annotation
- **WHEN** 生成第12章业务流程图
- **THEN** 系统 SHALL 每个节点标注 `方法名` + `文件路径:行号`，格式为 `ClassName.method()<br>path:line`

#### Scenario: Navigation chart code annotation
- **WHEN** 生成第10章主页面架构
- **THEN** 系统 SHALL 每个页面节点标注页面文件路径:行号

#### Scenario: ER diagram code annotation
- **WHEN** 生成第13章数据库设计
- **THEN** 系统 SHALL 在 ER 图下方附带表结构说明表，每表标注建表语句/Entity 类路径:行号

#### Scenario: Class diagram code annotation
- **WHEN** 生成第14章核心数据类关系图
- **THEN** 系统 SHALL 每个类标注文件路径:行号，关键类展示代码片段

### Requirement: Display key code snippets for all code-related steps
所有涉及代码的图表/章节 SHALL 在文档中展示对应的关键代码片段。

#### Scenario: Business flow code snippet
- **WHEN** 生成业务流程图中的核心步骤
- **THEN** 系统 SHALL 在步骤表下方展示对应的实现代码片段（3-15 行），标注文件路径和行号

#### Scenario: Key class code snippet
- **WHEN** 生成模块文档第11章关键类和方法
- **THEN** 系统 SHALL 为每个关键方法展示代码片段，包含功能说明注释

#### Scenario: Database table code snippet
- **WHEN** 生成第13章数据库设计
- **THEN** 系统 SHALL 展示建表 SQL 语句或 Entity 类代码片段

### Requirement: Generate build guide chapter
根文档第21章 SHALL 包含对应项目类型的编译指南。

#### Scenario: Android build guide
- **WHEN** 项目类型为 Android
- **THEN** 系统 SHALL 基于 `references/build-guide-android.md` 生成编译目标和命令

#### Scenario: HarmonyOS build guide
- **WHEN** 项目类型为 HarmonyOS Next
- **THEN** 系统 SHALL 基于 `references/build-guide-harmonyos.md` 生成编译目标和命令

### Requirement: Generate module documentation index
根文档第20章 SHALL 包含所有模块文档的索引链接。

#### Scenario: Module index generation
- **WHEN** 所有模块文档生成完成
- **THEN** 系统 SHALL 在第20章列出所有模块名称和对应文档的 Markdown 链接，模块名称和数量与第8章模块介绍一致

### Requirement: Handle document generation by strategy
系统 SHALL 根据 CP0 确认的策略执行不同的文档生成流程。codegraph 模式下所有可覆盖步骤 SHALL 强制走 codegraph。模块文档按拓扑排序分层生成。

#### Scenario: Strategy A full generation with codegraph
- **WHEN** 策略确认为 A（全量生成）且 codegraph 可用
- **THEN** 系统 SHALL 创建目录结构 → 生成章节级任务清单 → codegraph 执行所有分析 → 生成根文档（23章，codegraph 模式）→ 生成所有模块文档（14章，codegraph 模式）

#### Scenario: Strategy A full generation without codegraph
- **WHEN** 策略确认为 A（全量生成）且 codegraph 不可用
- **THEN** 系统 SHALL 执行完整 8 步手动扫描 → 生成根文档（21章）→ 生成所有模块文档（12章）→ 生成核心日志文档

#### Scenario: Strategy B supplement missing modules
- **WHEN** 策略确认为 B（补充生成）
- **THEN** 系统 SHALL 识别缺失模块 → 生成缺失模块的章节级任务清单 → 按任务清单逐章节补充生成 → 基于全部已有模块文档重新聚合生成根文档 → 同步补充缺失核心日志文档

#### Scenario: Strategy C incremental update
- **WHEN** 策略确认为 C（增量更新）
- **THEN** 系统 SHALL 对比代码 vs 文档时间戳 → 生成变更模块的章节级任务清单 → 按拓扑排序和任务清单逐章节更新 → 基于全部已有模块文档重新聚合生成根文档 → 同步更新核心日志文档

#### Scenario: Strategy D rebuild root doc
- **WHEN** 策略确认为 D（重建根文档）
- **THEN** 系统 SHALL 生成根文档聚合任务清单 → 基于已有模块文档聚合生成根文档 → 补充缺失模块文档 → 同步补充核心日志文档

#### Scenario: Resume from interrupted task list
- **WHEN** CP0 确认恢复执行
- **THEN** 系统 SHALL 读取已有 `.task-checklist.md` → 重置非终态任务 → 从第一个 `[ ]` 开始逐项执行

### Requirement: Execute generation with per-chapter progress tracking
系统 SHALL 在执行任务清单时逐章节追踪进度。

#### Scenario: Mark task as in-progress before execution
- **WHEN** 开始生成某模块的某章节
- **THEN** 系统 SHALL 先标记该任务为 `[>]` 并保存 `.task-checklist.md`，再开始执行

#### Scenario: Mark task as complete after successful execution
- **WHEN** 章节生成成功
- **THEN** 系统 SHALL 标记该任务为 `[x]` 并保存 `.task-checklist.md`

#### Scenario: Retry on first failure
- **WHEN** 章节生成失败
- **THEN** 系统 SHALL 标记为 `[1]` → 保存 → 立即重试一次 → 成功则标记 `[x]`，失败则标记 `[!]` 并记录原因

#### Scenario: Update module progress bar
- **WHEN** 某模块内一个章节任务完成
- **THEN** 系统 SHALL 更新该模块标题行的进度条（如 `[▰▰░░░░░░░░ 25%]`）

### Requirement: Root document section 12 aggregate all core features
根文档第12章 SHALL 全量汇总所有模块的核心功能，采用聚合展示模式，包含跨模块综合分析。

#### Scenario: Aggregate module-level core features
- **WHEN** 所有模块文档生成完毕
- **THEN** 系统 SHALL 在第12章 §12.1 生成跨模块整体业务概览图，§12.2 生成核心功能索引表，§12.3 按模块逐个展示核心功能（独立时序图+步骤详解表+代码片段），§12.4 分析跨模块端到端综合功能

#### Scenario: Core feature index table
- **WHEN** 聚合所有模块核心功能
- **THEN** 系统 SHALL 生成索引表列：功能ID | 功能名 | 所属模块 | 类型（模块/跨模块）| 本文档节号

#### Scenario: Cross-module end-to-end feature analysis
- **WHEN** 某业务流程跨越多个模块（如 下单=order+payment+inventory）
- **THEN** 系统 SHALL 在 §12.4 生成跨模块端到端时序图，参与者和步骤覆盖所有涉及的模块，标注每个步骤的所属模块和代码位置

### Requirement: Root document aggregates content from module documents
根文档中依赖模块文档内容的章节 SHALL 基于已生成的模块文档聚合，禁止凭空生成。

#### Scenario: Section 6 dependency graph aggregation
- **WHEN** 生成根文档第6章模块依赖关系图
- **THEN** 系统 SHALL 聚合所有模块文档第3章声明的依赖关系生成依赖图，确保依赖方向与模块文档一致

#### Scenario: Section 8 module introduction aggregation
- **WHEN** 生成根文档第8章模块介绍
- **THEN** 系统 SHALL 聚合所有模块文档第1章概述信息，模块列表与第20章索引一一对应

#### Scenario: Section 13 ER diagram aggregation
- **WHEN** 生成根文档第13章数据库设计
- **THEN** 系统 SHALL 聚合所有模块文档第7章数据依赖及实体信息生成 ER 图

#### Scenario: Section 14 class diagram aggregation
- **WHEN** 生成根文档第14章核心数据类关系图
- **THEN** 系统 SHALL 聚合所有模块文档第11章关键类信息生成类图


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

### Requirement: Generate log deficiency summary in root document
系统 SHALL 在 codegraph 模式下聚合所有模块日志的严重度评估结果，在根文档 §23 之后生成日志缺陷汇总章节。

#### Scenario: Aggregate severity statistics
- **WHEN** codegraph 可用且所有模块日志已生成
- **THEN** 系统 SHALL 在根文档日志缺陷汇总章输出全局统计：🔴严重 X 个 / 🟡警告 Y 个 / 🟢合格 Z 个

#### Scenario: List P0 critical defects
- **WHEN** 存在严重度为 🔴P0 的日志缺陷
- **THEN** 系统 SHALL 输出 P0 严重缺陷列表：调用链描述 | 涉及模块 | 覆盖率 | 详见链接（指向对应日志文档 §5）

#### Scenario: List P1 warnings
- **WHEN** 存在严重度为 🟡P1 的日志缺陷
- **THEN** 系统 SHALL 输出 P1 警告列表，附带简要说明和详见链接

#### Scenario: Manual mode skip
- **WHEN** codegraph 不可用
- **THEN** 系统 SHALL 标注"codegraph 不可用，日志缺陷评估跳过"
