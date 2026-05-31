## ADDED Requirements

### Requirement: Scan project directory structure
系统 SHALL 以深度≤5 递归扫描项目目录，识别所有模块。

#### Scenario: Standard multi-module project
- **WHEN** 项目包含多个子目录，每个子目录有独立的构建文件（如 `build.gradle`、`package.json`）
- **THEN** 系统 SHALL 识别每个含构建文件的子目录为独立模块

#### Scenario: Monorepo with nested packages
- **WHEN** 项目为 Monorepo 结构，`packages/` 下含多个独立包
- **THEN** 系统 SHALL 对每个包独立识别类型和模块，生成子根文档和统一索引

#### Scenario: Module with no source code
- **WHEN** 模块目录存在但无源码文件
- **THEN** 系统 SHALL 记录该模块为"无源码"并报告给用户，询问是否排除

### Requirement: Extract module dependencies from build files
系统 SHALL 解析项目构建文件以提取模块间的依赖关系。

#### Scenario: Extract Android Gradle dependencies
- **WHEN** 项目类型为 Android
- **THEN** 系统 SHALL 解析 `settings.gradle.kts` 中的 `include` 声明和各模块 `build.gradle` 的 `dependencies` 块

#### Scenario: Extract iOS CocoaPods dependencies
- **WHEN** 项目类型为 iOS 且使用 CocoaPods
- **THEN** 系统 SHALL 解析 `Podfile` 中的 `pod` 声明

#### Scenario: Extract JavaScript/TypeScript dependencies
- **WHEN** 项目类型为 Vue.js、React 或 Node.js
- **THEN** 系统 SHALL 解析 `package.json` 的 `dependencies` 和 `devDependencies` 字段

#### Scenario: Extract Flutter dependencies
- **WHEN** 项目类型为 Flutter
- **THEN** 系统 SHALL 解析 `pubspec.yaml` 的 `dependencies` 和 `dev_dependencies` 字段

#### Scenario: Extract HarmonyOS dependencies
- **WHEN** 项目类型为 HarmonyOS Next
- **THEN** 系统 SHALL 解析 `oh-package.json5` 和 `hvigorfile.ts` 中的依赖声明

#### Scenario: Build file parsing fails
- **WHEN** 构建文件不可解析（格式错误、权限不足）
- **THEN** 系统 SHALL 降级为目录结构推断模块关系，并记录警告

### Requirement: Generate dependency matrix
系统 SHALL 生成包含所有模块依赖关系的矩阵表格。

#### Scenario: Generate dependency matrix table
- **WHEN** 模块依赖提取完成
- **THEN** 系统 SHALL 输出包含"模块"、"依赖模块"、"依赖类型"三列的依赖矩阵表格

#### Scenario: Dependency type annotation
- **WHEN** 生成依赖矩阵
- **THEN** 系统 SHALL 使用标准依赖类型值（implementation/api/compileOnly/runtimeOnly/devDependencies/peerDependencies/embed/path）

### Requirement: Detect circular dependencies
系统 SHALL 检测模块间的循环依赖并输出报告。

#### Scenario: Circular dependency detected
- **WHEN** 依赖分析发现模块 A 依赖 B、B 依赖 C、C 依赖 A 的循环
- **THEN** 系统 SHALL 在依赖分析报告中标出循环路径，并给出解耦建议

#### Scenario: No circular dependencies
- **WHEN** 依赖分析未发现循环依赖
- **THEN** 系统 SHALL 在报告中明确标注"未发现循环依赖"

### Requirement: Generate Mermaid dependency graph
系统 SHALL 生成 Mermaid flowchart 格式的模块依赖关系图，每个节点标注构建文件路径和行号。

#### Scenario: Generate top-down dependency graph
- **WHEN** 依赖关系提取完成
- **THEN** 系统 SHALL 生成 `flowchart TD` 格式的依赖图，节点格式为 `NodeID["模块名<br>构建文件路径:行号"]`

#### Scenario: Dependency graph node with code location
- **WHEN** 生成依赖图中的模块节点
- **THEN** 系统 SHALL 在每个节点中标注构建文件路径和行号，如 `app["app 模块<br>app/build.gradle:1"]`

#### Scenario: Verify dependency graph consistency
- **WHEN** 依赖图和依赖矩阵生成完成
- **THEN** 系统 SHALL 确保依赖图中的边数 = 依赖矩阵行数，方向一致

### Requirement: Handle large module count
当模块过多时，系统 SHALL 采用分批处理策略。

#### Scenario: Project has more than 50 modules
- **WHEN** 项目模块数量超过 50
- **THEN** 系统 SHALL 按每批 ≤20 个模块分批处理依赖关系

#### Scenario: Project has 21-50 modules
- **WHEN** 项目模块数量在 21 到 50 之间
- **THEN** 系统 SHALL 正常处理但提示用户预计耗时较长（5-10 分钟）

### Requirement: Output topological sort grouping
系统 SHALL 在依赖分析完成后输出拓扑排序分组结果，供生成阶段使用。

#### Scenario: Output layer-by-layer module groups
- **WHEN** 依赖矩阵和循环依赖检测完成
- **THEN** 系统 SHALL 使用 Kahn 算法输出按依赖深度分层的模块分组：Layer 0（无依赖模块）, Layer 1, Layer 2, ...，同层模块无相互依赖

#### Scenario: Annotate circular dependency groups
- **WHEN** 存在循环依赖的模块组
- **THEN** 系统 SHALL 将循环依赖组标注为"循环依赖组-{ID}"，输出为虚拟层，包含组内所有模块

#### Scenario: Grouping output format
- **WHEN** 输出拓扑排序结果
- **THEN** 系统 SHALL 输出格式：层级编号 | 模块列表 | 依赖层 | 备注（循环依赖标注）
