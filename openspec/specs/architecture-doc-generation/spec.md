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
系统 SHALL 根据 CP0 确认的策略执行不同的文档生成流程。

#### Scenario: Strategy A full generation
- **WHEN** 策略确认为 A（全量生成）
- **THEN** 系统 SHALL 创建目录结构 → 生成根文档（21章）→ 生成所有模块文档（12章）→ 生成所有核心模块日志文档（5章）

#### Scenario: Strategy B supplement missing modules
- **WHEN** 策略确认为 B（补充生成）
- **THEN** 系统 SHALL 读取现有根文档 → 识别缺失模块 → 生成缺失模块文档 → 更新根文档 → 同步补充核心日志文档

#### Scenario: Strategy C incremental update
- **WHEN** 策略确认为 C（增量更新）
- **THEN** 系统 SHALL 对比代码 vs 文档时间戳 → 更新变化模块 → 新模块生成文档 → 同步更新核心日志文档 → 确保所有核心模块日志文档存在

#### Scenario: Strategy D rebuild root doc
- **WHEN** 策略确认为 D（重建根文档）
- **THEN** 系统 SHALL 基于已有模块文档重建根文档 → 补充缺失模块文档 → 同步补充核心日志文档
