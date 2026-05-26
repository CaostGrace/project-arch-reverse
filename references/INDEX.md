# references 目录索引

本文档是 `project-arch-reverse` skill 的参考文档入口，帮助你快速找到所需的规范或模板。

## 📚 按用途分类

### 入门必读

| 文件 | 说明 | 优先级 |
|------|------|--------|
| [quick-ref.md](quick-ref.md) | **快速参考**：执行预估、策略选择、常用命令 | ⭐⭐⭐ |
| [chapters-ref.md](chapters-ref.md) | **章节速查**：21章根文档 + 12章模块文档详情 + 5章核心日志文档 | ⭐⭐⭐ |

### 执行规范

| 文件 | 说明 | 使用场景 |
|------|------|----------|
| [mermaid-spec.md](mermaid-spec.md) | Mermaid 图表规范 | 生成依赖图、ER图、流程图、时序图 |
| [doc-format.md](doc-format.md) | 文档格式规范 | 表格格式、代码块规范、数据库设计格式 |
| [self-check-spec.md](self-check-spec.md) | 自检验证详细规格 | 检查点 2.5 执行依据 |
| [project-types-config.md](project-types-config.md) | 项目类型检测配置 | 检查点 1 项目类型识别 |
| [log-extraction-guide.md](log-extraction-guide.md) | 各语言日志提取指南 | 核心日志文档（5章）生成依据 |

### 文档模板

| 文件 | 适用项目 |
|------|----------|
| [template.md](template.md) | 通用模板 |
| [android-template.md](android-template.md) | Android |
| [ios-template.md](ios-template.md) | iOS |
| [java-backend-template.md](java-backend-template.md) | Java 后端 |
| [vue-template.md](vue-template.md) | Vue.js |
| [react-template.md](react-template.md) | React |
| [flutter-template.md](flutter-template.md) | Flutter |
| [nodejs-template.md](nodejs-template.md) | Node.js |
| [harmonyos-template.md](harmonyos-template.md) | HarmonyOS Next |

### 编译指南

| 文件 | 适用项目 |
|------|----------|
| [build-guide-android.md](build-guide-android.md) | Android |
| [build-guide-ios.md](build-guide-ios.md) | iOS |
| [build-guide-java-backend.md](build-guide-java-backend.md) | Java 后端 |
| [build-guide-vue.md](build-guide-vue.md) | Vue.js |
| [build-guide-react.md](build-guide-react.md) | React |
| [build-guide-flutter.md](build-guide-flutter.md) | Flutter |
| [build-guide-nodejs.md](build-guide-nodejs.md) | Node.js |
| [build-guide-harmonyos.md](build-guide-harmonyos.md) | HarmonyOS Next |

---

## 🎯 按检查点索引

| 检查点 | 必读文件 |
|--------|----------|
| **检查点 0** 增量检测 | [quick-ref.md](quick-ref.md)（执行策略选择） |
| **检查点 1** 项目类型确认 | [project-types-config.md](project-types-config.md) |
| **阶段: 项目扫描** | [quick-ref.md](quick-ref.md)（依赖提取命令）+ [mermaid-spec.md](mermaid-spec.md)（图表规范） |
| **阶段: 文档生成** | [chapters-ref.md](chapters-ref.md) + [doc-format.md](doc-format.md) + [log-extraction-guide.md](log-extraction-guide.md)（日志提取） |
| **检查点 2** 结果确认 | [self-check-spec.md](self-check-spec.md#检查点2结果确认流程) |
| **检查点 2.5** 自检验证 | [self-check-spec.md](self-check-spec.md) |
| **检查点 3** 人工更正 | - |

---

## 📖 按学习路径

### 新手路径
1. [quick-ref.md](quick-ref.md) - 了解 skill 能做什么
2. [chapters-ref.md](chapters-ref.md) - 了解文档结构
3. [doc-format.md](doc-format.md) - 了解输出格式

### 进阶路径
1. [mermaid-spec.md](mermaid-spec.md) - 精通图表生成
2. [self-check-spec.md](self-check-spec.md) - 掌握自检验证
3. [project-types-config.md](project-types-config.md) - 深度理解项目检测

### 专家路径
1. 各项目类型详细模板（*-template.md）
2. 各项目类型编译指南（build-guide-*.md）

---

## 🔗 文件关系图

```
SKILL.md (核心)
    │
    ├── quick-ref.md (快速入口)
    │       └── 执行预估 / 策略选择 / 常用命令
    │
    ├── chapters-ref.md (章节详情)
    │       └── 根文档 21 章 / 模块文档 12 章 / 核心日志文档 5 章
    │
    ├── mermaid-spec.md (图表规范)
    │       └── 依赖图 / ER图 / 流程图 / 时序图 / 类图
    │
    ├── doc-format.md (格式规范)
    │       └── 表格格式 / 代码块 / 数据库设计
    │
    ├── self-check-spec.md (自检验证)
    │       └── 循环机制 / 报告格式 / 决策点
    │
    ├── project-types-config.md (类型配置)
    │       └── 8 种项目类型检测
    │
    ├── log-extraction-guide.md (日志提取)
    │       └── 各语言日志模式 / 提取规则 / 调用链扫描
    │
    ├── template.md (通用模板)
    │       └── *-template.md (8 种项目类型模板)
    │
    └── build-guide-*.md (编译指南)
            └── 8 种项目类型编译命令
```
