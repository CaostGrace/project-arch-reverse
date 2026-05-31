## Context

project-arch-reverse v3.0 CP1.5 已集成 codegraph 的检测和安装，但缺少从 graph.db 到架构文档的分析转换管道。codegraph 提供结构化数据（节点角色、复杂度、调用树），需要一组明确的 Agent 指令将其转换为文档章节。

## Goals / Non-Goals

**Goals:**
- 定义 codegraph graph.db 到所有文档章节的完整映射
- 每个 core/entry 节点产出一份核心能力档案（用途+调用者+场景+时序图+流程图）
- 关键函数按复杂度+调用次数排序，带完整调用链
- 同时查缺补漏到模块文档和根文档的所有相关章节

**Non-Goals:**
- 不改变文档章节结构
- 不修改 codegraph 安装/构建流程
- 不替代生成阶段的聚合逻辑（聚合仍需要，但数据来源从手动变为 codegraph）

## Decisions

### D1: 核心能力档案模板

对 codegraph 识别的每个 core 节点（核心能力/关键函数），必须产出：

```markdown
### 核心能力: <函数名>

**用途**: <基于函数名+代码上下文推断的功能描述>
**所属模块**: <模块名>
**触发类型**: UI触发 / 系统触发 / 外部事件
**复杂度**: 认知 X | 圈 Y | 可维护性 Z
**代码位置**: <文件路径:行号>

**调用者**（谁调用了它）:
| 调用方 | 调用方式 | 触发类型 | 代码位置 |
|--------|---------|---------|----------|
| Activity.submit() | 直接调用 | UI触发 | OrderActivity.kt:56 |
| SyncWorker.doWork() | 间接(WI) | 系统触发 | SyncWorker.kt:23 |

**被调用者**（它调用了谁）:
| 被调用函数 | 调用方式 | 所在模块 | 代码位置 |
|-----------|---------|---------|----------|
| validateStock() | 直接 | order | OrderValidator.kt:12 |
| calculatePrice() | 直接 | order | PriceCalculator.kt:34 |
| saveToDB() | 直接 | data | OrderDao.kt:45 |

**当前场景**:
- 用户手动下单时触发 (OrderActivity)
- 支付成功后自动创建续订 (PaymentCallback)
- 定时同步外部订单 (SyncWorker)

**时序图** (从入口到核心到终点，≥6步):
[mermaid sequenceDiagram]

**业务流程图** (分支逻辑):
[mermaid flowchart]
```

### D2: codegraph 数据 → 文档章节映射

| 文档章节 | codegraph 数据源 | 产出方式 |
|----------|-----------------|---------|
| 根§6 依赖图 | 模块间调用边 | `codegraph plot --mermaid` + 节点标注模块 |
| 根§7 依赖分析 | SCC 检测结果 | 循环依赖报告 + 解耦建议 |
| 根§8 模块介绍 | 模块内 core 节点数 | 按 core 节点密度排序 |
| 根§12.3 核心功能 | 所有 core 节点的能力档案 | 聚合每个模块的档案 |
| 根§12.4 跨模块 | 跨模块调用边 | 从 entry 节点追踪完整调用链 |
| 根§13 ER图 | 类/接口节点 | codegraph 实体节点 → ER图 |
| 根§14 类图 | 高复杂度类节点 | 按复杂度 top N 类绘制类图 |
| 模块§1 概述 | 模块内 core/entry 节点概要 | 汇总核心能力数量和入口数量 |
| 模块§4 业务流程 | entry→core 调用链 | 从 entry 追踪到 core 的完整流程 |
| 模块§5 数据流 | flows_to/returns 边 | 数据流转时序图 |
| 模块§6 核心功能流程图 | 每个 core 节点的能力档案 | 独立时序图+步骤详解+代码 |
| 模块§11 关键类和方法 | top N by 复杂度+调用次数 | 属性面板+调用链+代码片段 |

### D3: MCP vs CLI 优先级

```
优先: codegraph mcp (33 tools, 结构化输出)
  └─ 不可用 →
         CLI: codegraph context <func> -T
         CLI: codegraph impact <entry>
         CLI: codegraph plot --mermaid
         CLI: codegraph audit <target>
```

### D4: 查缺补漏清单

对照 21+12+5 章文档，codegraph 无法覆盖的章节明确标注"降级为手动"：

| 章节 | codegraph 能力 | 状态 |
|------|:---:|------|
| 根§10 主页架构 | ❌ 页面布局 | 手动 |
| 根§11 页面跳转 | ❌ 路由导航 | 手动 |
| 根§15 ADR | ❌ 架构决策 | 手动 |
| 根§16 接口设计 | ❌ API 定义 | 手动 |
| 根§17 部署运维 | ❌ 基础设施 | 手动 |
| 根§18 安全设计 | ❌ 安全配置 | 手动 |
| 根§19 风险应对 | ❌ 风险评估 | 手动 |
| 模块§2 UI组件 | ❌ UI组件树 | 手动 |
| 模块§9 页面路由 | ❌ 路由配置 | 手动 |
| 模块§10 导航示例 | ❌ 导航代码 | 手动 |
| 模块§12 测试策略 | ❌ 测试覆盖 | 手动 |
