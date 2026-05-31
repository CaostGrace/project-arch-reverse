## Context

project-arch-reverse v3.0 的扫描阶段有 8 个手动步骤（grep/parse/scan）。`@optave/codegraph` v3.11.1 提供函数级依赖图、节点角色标记（entry/core/utility/adapter/dead/leaf）、复杂度度量，且支持 34 种语言。将其作为优先分析引擎可替代扫描阶段的大部分手动工作。

## Goals / Non-Goals

**Goals:**
- 扫描阶段优先使用 codegraph 数据作为分析源
- codegraph 不可用时完整降级到现有手动流程
- 自动检测和安装 codegraph，无需用户手动操作
- 代码有变更时，codegraph 增量重建（亚秒级）

**Non-Goals:**
- 不修改模块文档的章节结构
- 不改变根文档聚合逻辑
- 不改变 CP2/CP2.5/CP3 流程
- 不支持非 codegraph 覆盖语言的 MCP 集成（降级即可）

## Decisions

### D1: 两阶段检测 — 扫描前决策

```
CP0 增量检测 ──→ CP1 类型确认
                       │
              ┌────────▼────────┐
              │ codegraph 可用?  │
              │  1) npm ls      │
              │  2) graph.db    │
              └────────┬────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
    已装+DB        已装无DB         未安装
        │              │              │
     直接进入    codegraph       npm i -g
    分析模式    build             → build
        │              │              │
        └──────────────┼──────────────┘
                       │
              ┌────────▼────────┐
              │  构建成功?       │
              └────────┬────────┘
                       │
        ┌──────────────┼──────────────┐
        │ 成功                      │ 失败
        ▼                           ▼
   codegraph 模式              降级: 手动扫描
```

### D2: 分析数据映射

| 扫描步骤 | codegraph 数据源 | 替代方式 |
|----------|-----------------|---------|
| 目录扫描 (Step 1) | 保留手动 | — |
| 构建解析 (Step 2) | `codegraph impact` 模块间调用边 | MCP 工具 / CLI 输出 |
| 循环检测 (Step 3) | graph.db 中 SCC 查询 | codegraph check |
| 拓扑排序 (Step 4) | graph.db 依赖推导 | — |
| 入口点扫描 (Step 5) | 节点角色: entry | codegraph MCP query |
| 导航分析 (Step 6) | 保留手动 | 页面路由不在函数图 |
| 生成图表 (Step 7) | codegraph Mermaid 导出 | `codegraph plot --mermaid` |
| 加载模板 (Step 8) | 保留不变 | — |

**核心数据项映射**:
- 核心模块判定: 节点角色 core + 复杂度 > 阈值
- 核心功能: entry 节点 + 其调用链
- 关键类/方法: top N by 复杂度 + 调用次数
- 类图/ER图: Class/Interface 节点 + 继承边

### D3: 降级回退策略

```
降级触发条件:
  ├── codegraph 安装失败
  ├── codegraph build 失败
  ├── 项目语言不在已知 34 种支持范围
  └── graph.db 数据不完整（节点数 < 阈值）

降级行为:
  → 回退到当前完整 8 步手动扫描
  → 不报错，仅在 CP0 报告中标注"codegraph 不可用，使用手动分析"
  → 用户可在 CP0 强制指定"跳过 codegraph"
```

### D4: codegraph 安装方式

根据 GitHub README 推荐，使用 npm 全局安装：

```bash
npm install -g @optave/codegraph
cd <project> && codegraph build
```

安装检测：`npm ls -g @optave/codegraph` — 返回 0 即已安装。

## Risks / Trade-offs

| Risk | Mitigation |
|------|-----------|
| codegraph 构建大项目耗时 | 首次构建异步进行，后续增量 < 1 秒 |
| graph.db 格式变更导致解析失败 | 仅使用 CLI 标准输出和 MCP 工具，不直接读 SQLite |
| 用户环境中 npm 不可用 | npm 检测失败 → 降级，不影响正确性 |
| HarmonyOS ArkTS 语言支持不确定 | 先假设支持，构建失败自动降级 |
