# 项目架构逆向快速参考

本文件提供常用信息的快速查询，适用于执行时的快速参考。

## 快速导航

| 资源 | 说明 |
|------|------|
| [project-types-config.md](project-types-config.md) | 项目类型检测配置、分析流程 |
| [chapters-ref.md](chapters-ref.md) | 章节速查表（21章+12章+5章详情） |
| [mermaid-spec.md](mermaid-spec.md) | Mermaid 图表规范（完整版） |
| [doc-format.md](doc-format.md) | 文档格式规范（完整版） |
| [self-check-spec.md](self-check-spec.md) | 自检验证详细规格 |
| [log-extraction-guide.md](log-extraction-guide.md) | 各语言日志提取指南 |
| [template.md](template.md) | 通用模板 |
| [build-guide-*.md](build-guide-*.md) | 各项目类型编译指南 |

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
│   └─ 执行检查点2.5 自检验证（最多3次循环）
│
├─ "模块依赖"/"循环依赖"
│   └─ 跳过完整文档生成，只输出依赖矩阵 + 循环检测报告
│
└─ "项目类型"/"技术栈"
    └─ 只执行检查点1（项目类型检测），输出技术栈分析
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

| 策略 | 条件 | 操作 |
|------|------|------|
| A 全量生成 | docs/architecture/ 不存在 | 创建全部文档 |
| B 补充生成 | 根文档存在，模块文档部分缺失 | 生成缺失模块 |
| C 增量更新 | 根文档 + 所有模块文档均存在 | 对比更新 |
| D 重建根文档 | 根文档不存在，模块文档存在 | 重建根文档 |

### 日志文档策略（独立于架构文档）

> 核心模块 = 用户可达界面 + 后台运行能力 + 模块复杂度高 + 用户手动指定（满足任一即可）

| 策略 | 条件 | 操作 |
|------|------|------|
| L-A 全量生成 | 核心日志文档均不存在 | 为所有核心模块生成日志文档 |
| L-B 补充生成 | 部分核心模块缺少日志文档 | 生成缺失模块的日志文档 |
| L-C 增量更新 | 所有核心模块日志文档存在 | 对比代码时间戳，更新变化模块 |

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

> ⚠️ **重要**：所有模块的架构文档都必须生成，不可省略！核心模块的日志文档也**必须生成**。核心模块 = 用户可达界面 + 后台运行 + 模块复杂度高 + 用户手动指定（满足任一即可）。

## 常见易错点 ★

| 易错项 | 错误做法 | 正确做法 |
|--------|----------|----------|
| ER图实体名 | `news-resource`（含连字符） | `NEWS_RESOURCE`（下划线或无连字符） |
| 流程图节点形状 | `[(方法名())]`（圆柱体） | `["方法名()"]`（矩形，避免括号冲突） |
| 模块文档 | 跳过简单模块不生成 | **所有**模块必须生成模块文档 |
| 代码位置标注 | 只写文件名 | 必须写 `文件路径:行号` |
| 检查点0 | 跳过用户确认直接执行 | 必须展示检测报告并等待确认 |
| Mermaid subgraph | 中文名含特殊字符 | 使用字母/数字标识符 |
| 依赖表格格式 | 用 `---` 对齐列 | 使用 `---:` 或 `:---` 控制对齐 |
| 模块发现 | 只扫描根目录 build 文件 | 递归扫描到 depth≤5 |

## 输出质量检查点 ★

生成完成后、进入CP2前，快速检查3项：

| # | 检查 | 方法 | 不通过时 |
|---|------|------|---------|
| 1 | 依赖一致性 | 根文档第6章Mermaid图的边数 = 依赖矩阵行数 | 重新生成图表或矩阵，以构建文件解析为准 |
| 2 | 模块全覆盖 | `find . -name "build.gradle*"` 识别数 = 模块文档数 | 补充缺失的模块文档 |
| 3 | 代码位置可定位 | 随机抽查3个流程图节点，`文件路径:行号` 能在源码中找到对应方法 | 重新扫描标注 |
