## Why

当前核心功能判定仅覆盖 4 个通用大类（用户交互、后台运行、复杂度高、数据流转），缺乏平台特定的系统级入口识别。导致生成的时序图/业务流程图缺少真正的触发起点——例如 Android ContentProvider 触发的数据查询流程、Spring @Scheduled 定时任务、iOS Background Fetch 等。这些入口是系统框架回调或外部事件触发，不属于 UI 交互范畴，但往往是核心功能最重要的起点。

## What Changes

- **NEW**: 扫描阶段新增「入口点扫描」步骤，按项目类型搜索系统级入口
- **NEW**: `references/project-types-config.md` 新增「核心功能入口点」章节，为 8 种项目类型定义直接入口和间接入口的搜索模式
- **NEW**: 入口发现后，生成阶段自动将入口作为时序图的触发起点（标注触发类型：UI/系统/外部事件）
- **NEW**: 跨模块分析中，入口贯穿整个调用链——从系统入口开始追踪到终点

## Capabilities

### New Capabilities

- `entry-point-scanning`: 平台入口扫描 — 按项目类型加载入口搜索模式，对每个模块源码执行 grep，输出入口清单（方法名 + 文件:行号 + 触发类型），供时序图绘制使用

### Modified Capabilities

- `module-dependency-analysis`: 扫描阶段新增入口点扫描步骤；入口点作为分析调用链的起点
- `mermaid-chart-spec`: 时序图规范新增「入口标注」要求 — 系统触发入口必须标注触发条件（如 `Note over Sys: 触发条件`）
- `architecture-doc-generation`: 生成阶段受入口扫描影响 — 时序图从系统入口开始，跨模块流程追溯到原始入口

## Impact

- **references/project-types-config.md**: 新增「核心功能入口点」章节，为 Android/iOS/Java后端/Vue/React/Flutter/Node.js/HarmonyOS 各定义完整入口目录（直接入口 + 间接入口 + 搜索模式 + 触发类型 + 分析行为）
- **SKILL.md**: 扫描阶段插入入口点扫描步骤（Step 5），核心功能判定追加平台入口维度
- **references/mermaid-spec.md**: 时序图规范新增入口标注示例
