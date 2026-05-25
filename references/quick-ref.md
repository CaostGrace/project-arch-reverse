# 项目架构逆向快速参考

本文件提供常用信息的快速查询，适用于执行时的快速参考。

## 快速导航

| 资源 | 说明 |
|------|------|
| [project-types-config.md](project-types-config.md) | 项目类型检测配置、分析流程 |
| [chapters-ref.md](chapters-ref.md) | 章节速查表（21章+11章详情） |
| [mermaid-spec.md](mermaid-spec.md) | Mermaid 图表规范（完整版） |
| [doc-format.md](doc-format.md) | 文档格式规范（完整版） |
| [self-check-spec.md](self-check-spec.md) | 自检验证详细规格 |
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
| A 全量生成 | docs/ + docs/modules/ 均不存在 | 创建全部文档 |
| B 补充生成 | 根文档存在，模块文档部分缺失 | 生成缺失模块 |
| C 增量更新 | 根文档 + 所有模块文档均存在 | 对比更新 |
| D 重建根文档 | 根文档不存在，模块文档存在 | 重建根文档 |

### 日志文档策略（独立于架构文档）

| 策略 | 条件 | 操作 |
|------|------|------|
| L-A 全量生成 | 核心日志文档均不存在 | 为所有核心模块生成日志文档 |
| L-B 补充生成 | 部分核心模块缺少日志文档 | 生成缺失模块的日志文档 |
| L-C 增量更新 | 所有核心模块日志文档存在 | 对比代码时间戳，更新变化模块 |

## 增量检测命令

```bash
# 检查文档存在性
ls docs/*.项目架构文档.md 2>/dev/null || echo "根文档不存在"
find docs/modules/ -name "*模块架构.md" 2>/dev/null | head -20
find docs/modules/ -name "*_核心日志.md" 2>/dev/null | head -20

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
| 根文档 | `docs/` | `{项目名称}项目架构文档.md` |
| 模块文档 | `docs/modules/{模块}/` | `{模块名称}模块架构.md` |
| 核心日志文档 | `docs/modules/{模块}/` | `{模块名称}_核心日志.md` |

> ⚠️ **重要**：所有模块的架构文档都必须生成，不可省略！核心模块的日志文档也**必须生成**。
