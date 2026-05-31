## Context

project-arch-reverse v3.0 的扫描阶段有 7 步（目录扫描→构建解析→循环检测→拓扑排序→导航分析→生成图表→加载模板）。进入生成阶段后，时序图的起点通常从 UI 层或中间业务层开始，缺少系统级入口的发现和标注。8 种项目类型各自有不同的系统入口模式（ContentProvider、@Scheduled、AppDelegate 等），当前没有被搜索和分析。

## Goals / Non-Goals

**Goals:**
- 为 8 种项目类型定义完整的系统入口目录
- 扫描阶段增加入口点搜索步骤，自动发现每个模块的入口
- 入口按触发类型分类（UI触发 / 系统触发 / 外部事件）
- 时序图从发现的实际入口开始绘制，标注触发条件
- 跨模块流程中入口贯穿整条调用链

**Non-Goals:**
- 不改变模块文档的章节数量或结构
- 不新增项目类型
- 不对入口做运行时验证（纯静态分析）

## Decisions

### D1: 入口点分类模型

每种项目类型的入口分为两类 + 三种触发类型：

```
入口类型:
  ├── 直接入口（Direct）: 类型声明匹配
  │   extends ContentProvider, implements ApplicationListener
  │   grep 模式: 类继承/实现声明
  │
  └── 间接入口（Indirect）: 注解/API调用/生命周期钩子
      @Scheduled, @EventListener, mounted(), initState()
      grep 模式: 注解 + 方法签名

触发类型:
  ├── UI触发: onClick, onAppear, @Composable, mounted
  ├── 系统触发: WorkManager, Background Fetch, @Scheduled, Service
  └── 外部事件: FCM, @KafkaListener, URL Scheme, BroadcastReceiver
```

### D2: 入口目录存储位置

集中存储在 `references/project-types-config.md` 中，因为已经是按项目类型组织。新增 `## 核心功能入口点` 章节，包含 8 个子节（每种类型一个）。

每个入口条目格式：
```
| Provider | extends ContentProvider | `query\(` | 外部事件 | 从query/insert等方法向下追踪到数据层 |
```

### D3: 扫描步骤插入点

在扫描阶段的拓扑排序（Step 4）之后、导航分析（原 Step 5，现 Step 6）之前插入：

```
Step 5: 入口点扫描 ★ NEW
  1. 按项目类型从 project-types-config.md 加载入口目录
  2. 对每个模块源码执行 grep 搜索
  3. 输出: {模块 → [入口清单]}
     每个入口包含: 方法名 | 文件:行号 | 入口类型 | 触发类型
```

### D4: 时序图入口标注规范

系统触发入口在时序图首步必须标注触发条件：

```mermaid
Note over Sys: 系统触发: 网络可用 / 定时任务 / FCM推送
Sys->>EntryPoint: method()
```

UI 触发入口标注交互组件：
```mermaid
Note over UI: UI触发: 按钮点击 / 页面进入
UI->>VM: onClick()
```

## Risks / Trade-offs

| Risk | Mitigation |
|------|-----------|
| 入口模式过于具体，部分项目变体无法匹配 | 优先匹配通用模式（如 `extends Service`），平台变体用 OR 组合 |
| grep 搜索可能匹配到注释/死代码 | 过滤掉 `//` 和 `/*` 开头的行，标注"待确认" |
| 入口数量过多导致时序图过于冗长 | 非核心模块入口仅列举不展开；核心模块入口才绘制完整时序 |
