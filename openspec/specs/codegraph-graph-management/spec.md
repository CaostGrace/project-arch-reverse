## ADDED Requirements

### Requirement: Detect codegraph availability
系统 SHALL 在 CP1 之后扫描阶段之前检测 codegraph 的安装状态和图数据库。

#### Scenario: codegraph installed and graph.db exists
- **WHEN** `npm ls -g @optave/codegraph` 返回 0 且 `.codegraph/graph.db` 存在
- **THEN** 系统 SHALL 标记 codegraph 为"可用"，进入 codegraph 分析模式

#### Scenario: codegraph installed but no graph.db
- **WHEN** codegraph 已安装但 `.codegraph/graph.db` 不存在
- **THEN** 系统 SHALL 执行 `codegraph build` 构建知识图谱，成功后进入分析模式

#### Scenario: codegraph not installed
- **WHEN** `npm ls -g @optave/codegraph` 返回非 0
- **THEN** 系统 SHALL 执行 `npm install -g @optave/codegraph` 安装，安装成功后执行 `codegraph build`

#### Scenario: codegraph installation fails
- **WHEN** npm install 失败（npm 不可用或网络错误）
- **THEN** 系统 SHALL 在 CP0 报告中标注"codegraph 安装失败"→ 降级为手动扫描模式

#### Scenario: codegraph build fails
- **WHEN** `codegraph build` 执行失败（语言不支持或解析错误）
- **THEN** 系统 SHALL 标注"codegraph 构建失败"→ 降级为手动扫描模式

#### Scenario: user opts out
- **WHEN** 用户在 CP0 阶段指定"跳过 codegraph"
- **THEN** 系统 SHALL 直接进入手动扫描模式，不尝试 codegraph

### Requirement: Use codegraph data for dependency analysis
系统 SHALL 在 codegraph 可用时优先使用其数据替代手动依赖分析。

#### Scenario: Query inter-module dependencies
- **WHEN** codegraph 可用且需生成依赖矩阵
- **THEN** 系统 SHALL 从 codegraph 查询模块间的调用边，整理为依赖矩阵表格

#### Scenario: Detect circular dependencies
- **WHEN** codegraph 可用且需检测循环依赖
- **THEN** 系统 SHALL 使用 codegraph 的图分析能力识别强连通分量（SCC）

#### Scenario: Export dependency graph as Mermaid
- **WHEN** codegraph 可用且需生成第6章依赖图
- **THEN** 系统 SHALL 使用 `codegraph plot --mermaid` 导出 Mermaid 格式依赖图

### Requirement: Use codegraph node roles for entry and core identification
系统 SHALL 使用 codegraph 的节点角色标记识别入口点和核心功能。

#### Scenario: Identify entry points from node roles
- **WHEN** codegraph 可用且需扫描入口点
- **THEN** 系统 SHALL 查询所有 role=entry 的节点，作为核心功能入口清单

#### Scenario: Identify core capabilities from node roles
- **WHEN** codegraph 可用且需判定核心功能
- **THEN** 系统 SHALL 查询所有 role=core 的节点 + 复杂度指标，合并为平台入口触发的核心功能

#### Scenario: Identify key classes and methods
- **WHEN** codegraph 可用且需生成模块文档第11章
- **THEN** 系统 SHALL 按复杂度/调用次数排序 top N 节点，补充文件路径和代码片段

### Requirement: Fall back to manual scanning when codegraph unavailable
系统 SHALL 在 codegraph 不可用时完整回退到手动扫描流程。

#### Scenario: Fall back on build failure
- **WHEN** codegraph 构建失败
- **THEN** 系统 SHALL 执行完整的 8 步手动扫描（grep + 构建文件解析 + 入口正则），流程与 codegraph 不可用时一致

#### Scenario: Fall back on unsupported language
- **WHEN** 项目语言不在 codegraph 支持的 34 种语言范围内
- **THEN** 系统 SHALL 标注"语言不支持"→ 降级手动扫描，不以错误终止

#### Scenario: Fall back on incomplete graph data
- **WHEN** graph.db 节点数 < 10（异常低）
- **THEN** 系统 SHALL 标注"图数据不完整"→ 降级手动扫描
