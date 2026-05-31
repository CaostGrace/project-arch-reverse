# 项目架构逆向快速参考

本文件提供常用信息的快速查询，适用于执行时的快速参考。

## 快速导航

| 资源 | 说明 |
|------|------|
| [project-types-config.md](project-types-config.md) | 项目类型检测配置、分析流程 |
| [chapters-ref.md](chapters-ref.md) | 章节速查表（21章+12章+5章详情 + 函数/场景级日志章节） |
| [mermaid-spec.md](mermaid-spec.md) | Mermaid 图表规范（完整版） |
| [doc-format.md](doc-format.md) | 文档格式规范（完整版） |
| [self-check-spec.md](self-check-spec.md) | 自检验证详细规格 |
| [log-extraction-guide.md](log-extraction-guide.md) | 各语言日志提取指南 |
| [template.md](template.md) | 通用模板 |
| [android-template.md](android-template.md) | Android 项目模板 |
| [ios-template.md](ios-template.md) | iOS 项目模板 |
| [java-backend-template.md](java-backend-template.md) | Java 后端项目模板 |
| [vue-template.md](vue-template.md) | Vue.js 项目模板 |
| [react-template.md](react-template.md) | React 项目模板 |
| [flutter-template.md](flutter-template.md) | Flutter 项目模板 |
| [nodejs-template.md](nodejs-template.md) | Node.js 项目模板 |
| [harmonyos-template.md](harmonyos-template.md) | HarmonyOS Next 项目模板 |
| [build-guide-android.md](build-guide-android.md) | Android 编译指南 |
| [build-guide-ios.md](build-guide-ios.md) | iOS 编译指南 |
| [build-guide-java-backend.md](build-guide-java-backend.md) | Java 后端编译指南 |
| [build-guide-vue.md](build-guide-vue.md) | Vue.js 编译指南 |
| [build-guide-react.md](build-guide-react.md) | React 编译指南 |
| [build-guide-flutter.md](build-guide-flutter.md) | Flutter 编译指南 |
| [build-guide-nodejs.md](build-guide-nodejs.md) | Node.js 编译指南 |
| [build-guide-harmonyos.md](build-guide-harmonyos.md) | HarmonyOS Next 编译指南 |

## 执行预估

| 项目规模 | 模块数量 | 预计耗时 | 说明 |
|----------|----------|----------|------|
| 小型 | ≤5 | 1-2分钟 | 快速扫描 |
| 中型 | 6-20 | 3-5分钟 | 标准分析 |
| 大型 | 21-50 | 5-10分钟 | 分批处理 |
| 超大 | >50 | >10分钟 | 拆分为多个模块分别分析 |

> 💡 大型项目建议分批处理，每批≤20个模块

## 请求→策略决策树 ★

用户请求关键词分类，直接映射到最佳执行策略：

```
用户请求:
├─ "分析项目"/"生成架构文档"/"新人入职"
│   └─ 检查 docs/architecture/ 是否存在
│       ├─ 不存在 → [A] 全量生成 + [L-A] 全量日志
│       ├─ 存在但有缺模块 → [B] 补充生成 + [L-B] 补充日志
│       └─ 全部存在 → [C] 增量更新 + [L-C] 对比更新日志
│
├─ "更新文档"/"代码有变化"
│   └─ [C] 增量更新 + [L-C] 对比更新日志
│
├─ "新增了X模块"/"补上文档"
│   └─ 检查根文档是否存在
│       ├─ 存在 → [B] 补充生成缺失模块
│       └─ 不存在 → [A] 全量生成
│
├─ "检查文档"/"自检"/"文档完整性"
│   └─ 执行 CP6 自检验证（硬门禁，全部通过为止）
│
├─ "模块依赖"/"循环依赖"
│   └─ 跳过完整文档生成，只输出依赖矩阵 + 循环检测报告
│
└─ "项目类型"/"技术栈"
    └─ 只执行检查点1（项目类型检测），输出技术栈分析

### 混合项目/Monorepo 决策 ★

```
混合项目检测:
├─ 单一项目类型 → 正常流程
├─ 多类型签名（如 Android + Flutter 插件）
│   └─ 识别主类型 + 子类型 → 用户确认模块划分 → 分别使用对应模板
└─ Monorepo（如 packages/ 下多个独立项目）
    └─ 每个包独立识别类型+模块 → 生成子根文档 → 根文档含统一索引
```
```

## 何时使用

### 推荐场景

| 场景 | 使用 | 说明 |
|------|------|------|
| 分析项目结构 | ✅ | 快速了解项目整体架构 |
| 生成架构文档 | ✅ | 为团队提供项目文档 |
| 检测模块依赖 | ✅ | 识别循环依赖和优化机会 |
| 分析页面导航 | ✅ | 理解页面跳转关系 |
| 新人入职 | ✅ | 帮助新成员快速上手 |
| AI Agent 分析 | ✅ | 辅助 AI 理解代码库 |
| 项目重构前 | ✅ | 评估重构影响范围 |
| 自检架构文档 | ✅ | 验证文档完整性 |

### 不适用场景

| 场景 | 使用 |
|------|------|
| 代码编写/重构 | ❌ |
| Bug 修复 | ❌ |

### 典型使用示例

| 示例 | 用户输入 |
|------|----------|
| 新人入职 | "帮我分析一下这个项目的结构，我是新来的" |
| 文档更新 | "这个项目已经有架构文档了，帮我更新一下" |
| 架构评审 | "帮我检查一下项目中是否有循环依赖" |
| 专项分析 | "帮我分析这个Android项目的模块依赖关系" |
| 自检验证 | "帮我检查架构文档是否完整" |

## 执行策略选择

> 🔄 **模块优先原则**：所有策略（A/B/C）均先按拓扑排序生成模块文档，再聚合生成根文档。
> 📋 **任务清单机制**：生成阶段前创建章节级 `.task-checklist.md`，逐章执行并持久化进度。中断后可从第一个未完成任务恢复。
> 🧠 **codegraph 优先**：CP2 自动检测 `@optave/codegraph`，可用时替代手动扫描步骤；不可用时降级为手动模式。

## codegraph 命令速查

```bash
# 安装
npm install -g @optave/codegraph

# 构建知识图谱（首次全量，后续增量 < 1s）
cd <project> && codegraph build

# 检测是否已安装
npm ls -g @optave/codegraph

# Mermaid 导出
codegraph plot --mermaid

# 参考资料
# https://github.com/optave/ops-codegraph-tool
```

### codegraph 降级决策树

```
CP2 codegraph 检测:
├── npm --version
│   ├── npm 可用 → 继续
│   │   ├── npm ls -g @optave/codegraph 返回 0
│   │   │   ├── .codegraph/graph.db 存在 → ✅ codegraph 分析模式
│   │   │   └── graph.db 不存在 → codegraph build
│   │   │       ├── 成功 → ✅ codegraph 分析模式
│   │   │       └── 失败 → ⚠️ 降级: 手动 8 步扫描
│   │   └── npm ls 返回非 0 → npm install -g @optave/codegraph
│   │       ├── 成功 → codegraph build → (同上)
│   │       └── 失败 → ⚠️ 降级: 手动 8 步扫描
│   └── npm 不可用 → 询问"是否安装 Node.js？"
│       ├── 用户同意 → 安装 Node.js → 继续 codegraph 检测
│       └── 用户拒绝 → ⚠️ 降级: 手动 8 步扫描
└── 用户 CP0 指定"跳过 codegraph" → ⚠️ 强制手动扫描
```

| 策略 | 条件 | 操作（模块→根） |
|------|------|-----------------|
| A 全量生成 | docs/architecture/ 不存在 | 创建任务清单 → 拓扑排序分层逐章生成所有模块文档 → 聚合生成根文档 |
| B 补充生成 | 根文档存在，模块文档部分缺失 | 创建缺失模块任务清单 → 拓扑排序逐章补充 → 重新聚合生成根文档 |
| C 增量更新 | 根文档 + 所有模块文档均存在 | 创建变更模块任务清单 → 拓扑排序逐章更新 → 重新聚合生成根文档 |
| D 重建根文档 | 根文档不存在，模块文档存在 | 创建聚合任务清单 → 基于已有模块文档聚合重建根文档 |

### 日志文档策略（与架构文档联动，不可独立跳过）

> 日志触发模块 = 核心模块 ∪ codegraph 高复杂度函数所在模块 ∪ codegraph 关键调用链模块
> 日志触发阈值：认知复杂度 > 10 或 被调用次数 > 3（codegraph 精确值）
> 手动模式仅核心模块触发，跳过第二层日志

| 策略 | 条件 | 操作 |
|------|------|------|
| L-A 全量生成 | 日志触发模块列表的日志文档均不存在 | 为日志触发模块列表生成第一层+第二层日志 |
| L-B 补充生成 | 部分模块缺少日志文档 | 生成缺失模块的第一层+第二层日志 |
| L-C 增量更新 | 所有日志文档存在 | 对比代码时间戳，更新变化模块的第一层+第二层日志 |

### 混合项目策略（多类型项目）

| 场景 | 策略 | 操作 |
|------|------|------|
| Android + Flutter 插件 | 主项目A + 子项目B | 主项目用 android-template，插件部分用 flutter-template，根文档统一索引 |
| Monorepo（前端+后端） | 分区生成 | 每个包独立生成架构文档，根文档含全局模块依赖图 |
| 用户手动指定类型 | 以用户为准 | 忽略自动检测结果，使用用户指定类型的模板和配置 |

### 恢复执行流程 ★
> 当 `.task-checklist.md` 存在且未完成时，CP0 自动推荐恢复执行。

```
CP0 检测到未完成任务清单
        │
        ├─ 展示：上次中断于 {模块§X}，{N} 项待完成
        │
        ├─ 用户选择
        │     ├─ [1] 恢复执行 → 跳转生成阶段，从第一个 [ ] 开始
        │     └─ [2] 放弃重新开始 → 删除 .task-checklist.md → 策略 A 全量
        │
        ▼
  任务执行阶段：读取 .task-checklist.md
      │  - 将所有 [>]/[1] 重置为 [ ]
      │  - 所有 [x]/[!] 保持不变
      │  - 从第一个 [ ] 开始逐项执行
      │  - 每步标记 [x] 并保存文件
      ▼
  全部 [x]/[!] → 进入 CP2
```

### 拓扑排序生成说明 ★

> 扫描阶段使用 **Kahn 算法** 对模块按依赖深度分层，控制生成顺序。

```
依赖矩阵:           拓扑排序分组:          生成顺序:
A → C              Layer 0: [C, D]       1. 并行生成 C, D 模块文档
B → C              Layer 1: [A, B]       2. 并行生成 A, B 模块文档
C → D              Layer 2: [E]          3. 生成 E 模块文档
D → (无)           (循环依赖: 合并同层)    4. 聚合生成根文档
E → A, B
```

- **同层模块**：互不依赖，可并行生成（逻辑分组，Agent 串行执行）
- **循环依赖**：A⇄B 形成环时合并为虚拟层 [A, B]，同层处理
- **顺序保证**：下层生成时，上层模块文档已就绪，可引用其内容

## 增量检测命令

```bash
# 检查文档存在性
ls docs/architecture/*.项目架构文档.md 2>/dev/null || echo "根文档不存在"
find docs/architecture/modules/ -name "*模块架构.md" 2>/dev/null | head -20
find docs/logs/ -name "*_核心日志.md" 2>/dev/null | head -20

# Android 项目
find . -maxdepth 3 -name "settings.gradle*" -o -name "build.gradle*" | head -5
find . -path "*/app/build.gradle*" -o -path "*/:core/*/build.gradle*" | head -10

# iOS 项目
find . -maxdepth 3 \( -name "*.xcworkspace" -o -name "Podfile" -o -name "*.swift" \) | head -10

# Vue/React 项目
find . -maxdepth 3 \( -name "package.json" -o -name "vite.config.*" -o -name "webpack.config.*" \) | head -5

# Java 后端
find . -maxdepth 3 \( -name "pom.xml" -o -name "build.gradle*" \) | head -5

# Flutter
find . -maxdepth 3 -name "pubspec.yaml" | head -5

# HarmonyOS
find . -maxdepth 3 \( -name "hvigorfile.ts" -o -name "oh-package.json5" \) | head -5
```

## 模块依赖提取命令

```bash
# Android (Gradle) - 依赖提取
./gradlew :app:dependencies --configuration releaseRuntimeClasspath 2>/dev/null | grep "^\\---" | head -30

# 循环依赖检测
./gradlew dependencyInsight --configuration releaseRuntimeClasspath --dependency :core:data 2>/dev/null

# iOS (CocoaPods)
cat Podfile | grep -E "^pod|^use_frameworks"
xcodebuild -workspace *.xcworkspace -list 2>/dev/null | head -20

# Web 项目 (package.json)
cat package.json | grep -E '"dependencies"|"devDependencies"' -A 30 | grep -E '"@|' | head -20
```

## 文档输出位置

| 类型 | 路径 | 命名 |
|------|------|------|
| 根文档 | `docs/architecture/` | `{项目名称}项目架构文档.md` |
| 模块文档 | `docs/architecture/modules/{模块}/` | `{模块名称}模块架构.md` |
| 核心日志文档 | `docs/logs/{模块}/` | `{模块名称}_核心日志.md` |
| 函数级日志 | `docs/logs/{模块}/` | `{函数名}_关键函数日志.md` |
| 场景日志 | `docs/logs/{模块}/` | `{场景名}_场景日志.md` |

> ⚠️ **重要**：所有模块的架构文档都必须生成，不可省略！日志触发模块列表的日志文档也**必须生成**。日志触发模块 = 核心模块 ∪ codegraph 高复杂度函数所在模块 ∪ codegraph 关键调用链模块（满足任一即可）。

## 常见易错点 ★

| 易错项 | 错误做法 | 正确做法 |
|--------|----------|----------|
| ER图实体名 | `news-resource`（含连字符） | `NEWS_RESOURCE`（下划线或无连字符） |
| 流程图节点形状 | `[(方法名())]`（圆柱体） | `["方法名()"]`（矩形，避免括号冲突） |
| 模块文档 | 跳过简单模块不生成 | **所有**模块必须生成模块文档 |
| 代码位置标注 | 只写文件名 | 必须写 `文件路径:行号` |
| 代码位置标注范围 | 仅流程图/时序图标注 | **所有图表**（依赖图、流程图、时序图、导航图、类图、ER图）涉及代码的节点都必须标注 |
| 关键代码展示 | 标注为"推荐"，部分步骤省略 | **必需**：所有关键代码步骤都必须在文档中展示代码片段 |
| 依赖图节点 | `ModuleA[模块A]` 无代码位置 | `ModuleA["模块A<br>build.gradle:1"]` |
| ER图表位置 | ER图内无代码位置 | ER图下方附带表结构说明表，标注建表/Entity代码位置 |
| 类图节点 | 类图中无代码位置 | 类图下方附带类说明表，标注文件路径:行号 |
| 检查点0 | 跳过用户确认直接执行 | 必须展示检测报告并等待确认 |
| Mermaid subgraph | 中文名含特殊字符 | 使用字母/数字标识符 |
| 依赖表格格式 | 用 `---` 对齐列 | 使用 `---:` 或 `:---` 控制对齐 |
| 模块发现 | 只扫描根目录 build 文件 | 递归扫描到 depth≤5 |
| 流程图未定义节点 | 箭头引用未声明的节点ID | 所有节点ID必须先定义再引用 |
| 节点文本裸括号 | `["(MySQL...)"]` | 去掉首尾裸括号，改为 `["MySQL..."]` |
| 节点括号不匹配 | 文本末尾多余 `)` | 检查括号成对匹配 |
| 时序图跳层 | `Store->>API` 跳过 Service | 遵循分层 UI→Service→API |
| 时序图粗略 | 只有3-4步的简化时序图 | ≥6步、≥4参与者、完整分层、有返回消息 |
| 多功能合并 | 多个核心功能合为一个时序图 | 每个核心功能/能力必须有独立的时序图 |
| 混合项目 | 使用单一模板覆盖所有子项目 | 主类型+子类型分别使用对应模板 |
| Monorepo | 将所有包合并为一个项目文档 | 每个包独立生成文档，根文档统一索引 |
| 用户覆盖 | 忽略用户手动指定类型 | 以用户指定为准，使用指定类型的配置和模板 |

## 输出质量检查点 ★

生成完成后、进入CP2前，快速检查3项：

| # | 检查 | 方法 | 不通过时 |
|---|------|------|---------|
| 1 | 依赖一致性 | 根文档第6章Mermaid图的边数 = 依赖矩阵行数 | 重新生成图表或矩阵，以构建文件解析为准 |
| 2 | 模块全覆盖 | `find . -name "build.gradle*"` 识别数 = 模块文档数 | 补充缺失的模块文档 |
| 3 | 代码位置可定位 | 随机抽查3个流程图节点，`文件路径:行号` 能在源码中找到对应方法 | 重新扫描标注 |

