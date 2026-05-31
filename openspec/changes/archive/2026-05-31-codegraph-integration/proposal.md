## Why

当前项目架构分析完全依赖 Agent 手动 grep + 文本扫描（构建文件解析、入口点正则搜索、人工判断依赖关系）。这种方式在大型项目中效率低、准确度有限，且无法获取函数级调用链和复杂度度量。`@optave/codegraph` 是一个开源代码知识图谱工具，能自动构建函数级依赖图、识别 entry/core 节点、提供复杂度指标，并支持 Mermaid/MCP 导出。将其作为优先分析引擎可大幅提升分析精度和效率。

## What Changes

- **NEW**: CP0 新增 codegraph 可用性检测 — 检查 `npm ls @optave/codegraph` 和 `.codegraph/graph.db`
- **NEW**: 自动安装 — 若未安装，执行 `npm install -g @optave/codegraph`（基于 GitHub 仓库安装指南）
- **NEW**: 自动构建 — 若无 graph.db 或过期，执行 `codegraph build`
- **NEW**: codegraph 分析模式 — 用 graph.db 数据替代扫描阶段的多个手动步骤（依赖、入口、核心功能识别）
- **NEW**: 降级回退 — codegraph 不可用（语言不支持/构建失败）时完整回退到当前手动扫描流程

## Capabilities

### New Capabilities

- `codegraph-graph-management`: codegraph 知识图谱的生命周期管理 — 检测可用性、安装、构建、刷新

### Modified Capabilities

- `incremental-detection`: CP0 新增 codegraph 可用性检测步骤
- `module-dependency-analysis`: 扫描阶段优先使用 codegraph 查询依赖关系和循环检测
- `entry-point-scanning`: 优先使用 codegraph 的 entry 节点角色识别入口
- `architecture-doc-generation`: 核心功能/核心能力判定优先使用 codegraph 的 core 节点+复杂度指标
- `topological-sort-generation`: 拓扑排序输入优先来自 codegraph 的依赖图

## Impact

- **SKILL.md**: CP0 新增 codegraph 检测；扫描阶段新增 codegraph 优先模式说明；降级回退规则
- **references/quick-ref.md**: 新增 codegraph 安装命令和降级决策树
- **references/project-types-config.md**: 标注每种项目类型的 codegraph 语言覆盖状态
