# 项目架构逆向工程 (project-arch-reverse)

逆向分析项目架构的 Skill，支持检测项目类型（Android/iOS/Java后端、Vue、React、HarmonyOS Next等），自动生成含模块依赖图、数据库设计、页面导航的架构文档。**强制生成所有模块文档，不可省略**。支持增量更新、人工更正和自检验证（最多3次循环）。

## 功能特性

- **自动检测项目类型**：识别 Android、iOS、Java后端、Vue、React、Flutter、Node.js、HarmonyOS Next 等项目
- **模块依赖分析**：提取模块依赖关系，检测循环依赖
- **可视化文档生成**：生成 Mermaid 图表（依赖图、ER图、流程图）
- **页面导航分析**：分析路由配置和页面跳转关系
- **强制生成所有模块文档**：不可省略任何模块
- **自检验证**：生成后自动检查文档完整性、依赖关系正确性、模块文档生成状态（最多3次循环）
- **增量更新**：智能检测已有文档，只更新变化部分
- **人工更正**：支持生成后的人工审核和更正

## 快速开始

### 触发方式

在对话中触发本 Skill：

```
分析项目结构
生成架构文档
项目架构
逆向分析
生成项目文档
模块架构文档
架构逆向
项目分析
自检架构文档
检查架构完整性
```

### 执行流程

```
检查点0（增量检测） → 检查点1（项目类型确认） → 检查点2（结果确认）
                                                                      ↓
                                        检查点2.5（自检验证，必须，最多3次循环）
                                                                      ↓
                                                            检查点3（人工更正，可选）
```

**自检验证机制**：
- 自检过程中发现问题立即处理，处理后重新验证
- 最多循环3次，确保问题得到解决
- 3次后仍存在的问题会记录到最终报告，移交人工更正处理

### 输出文档

| 类型 | 路径 | 命名 |
|------|------|------|
| 根文档 | `docs/` | `{项目名称}项目架构文档.md` |
| 模块文档 | `<模块>/docs/` | `{模块名称}模块架构.md` |

> ⚠️ **重要**：所有模块的架构文档都必须生成，不可省略！

## 目录结构

```
project-arch-reverse/
├── SKILL.md                    # 核心 Skill 定义（精简版）
├── README.md                   # 本文件
└── references/
    ├── quick-ref.md            # 快速参考（执行预估、场景、策略）
    ├── chapters-ref.md          # 章节速查表（21章+11章详情）
    ├── mermaid-spec.md          # Mermaid 图表规范（含示例代码）
    ├── doc-format.md            # 文档格式规范（表格、代码块格式）
    ├── self-check-spec.md       # 自检验证详细规格（报告格式、决策点）
    ├── project-types-config.md  # 项目类型检测配置
    ├── template.md              # 通用模板
    ├── build-guide-*.md        # 编译指南（8个项目类型）
    └── *-template.md           # 各项目类型详细模板
```

## 支持的项目类型

| 项目类型 | 检测文件 | 文档模板 |
|----------|----------|----------|
| Android | build.gradle, AndroidManifest.xml | android-template.md |
| iOS | .xcworkspace, .swift | ios-template.md |
| Java 后端 | pom.xml, Spring Boot | java-backend-template.md |
| Vue.js | package.json, .vue | vue-template.md |
| React | package.json, .jsx/.tsx | react-template.md |
| Flutter | pubspec.yaml | flutter-template.md |
| Node.js | package.json, server.js | nodejs-template.md |
| HarmonyOS Next | hvigorfile.ts, oh-package.json5 | harmonyos-template.md |

## 编译指南

各项目类型的编译目标、用途和命令详见：

- [Android 编译指南](references/build-guide-android.md)
- [iOS 编译指南](references/build-guide-ios.md)
- [Java 后端编译指南](references/build-guide-java-backend.md)
- [Vue.js 编译指南](references/build-guide-vue.md)
- [React 编译指南](references/build-guide-react.md)
- [Flutter 编译指南](references/build-guide-flutter.md)
- [Node.js 编译指南](references/build-guide-nodejs.md)
- [HarmonyOS Next 编译指南](references/build-guide-harmonyos.md)

## 文档章节

### 根文档（21章）

1. **文档修订历史**：记录文档版本变更
2. **项目架构概览**：整体架构描述
3. **项目简介**：项目背景和目标
4. **术语与缩略语**：关键术语定义
5. **技术栈**：使用的技术和框架
6. **模块依赖关系图**：Mermaid可视化依赖图
7. **依赖关系分析**：模块依赖详情和循环检测
8. **模块简略介绍**：各模块职责概述
9. **主要组件功能介绍**：核心组件说明
10. **主页面架构**：页面结构和布局
11. **页面跳转详情**：路由配置和页面关系
12. **主要业务流程图**：整体业务流程和时序图
13. **数据库设计**：ER图、数据表结构（含外键/索引）
14. **核心数据类关系图**：类图和数据模型
15. **架构决策记录(ADR)**：关键设计决策
16. **接口设计**：API接口规范
17. **部署与运维方案**：部署架构和运维策略
18. **安全设计**：安全措施和防护
19. **风险与应对措施**：风险评估和应对方案
20. **模块文档索引**：各模块文档链接
21. **项目编译指南**：编译目标和命令

### 模块文档（11章）

1. **模块概述**：模块定位和职责
2. **依赖关系**：依赖的模块和库
3. **业务流程**：模块内业务流程
4. **数据流**：数据流转过程
5. **核心功能流程图**：核心功能的流程和时序图
6. **数据依赖关系**：与其他模块的数据交互
7. **依赖场景说明**：依赖其他模块的场景
8. **页面与路由**：模块内页面和路由
9. **导航调用示例**：页面跳转参数和示例
10. **关键类和方法**：核心类和方法说明
11. **测试策略**：测试方法和覆盖

## 自检验证

生成文档后，必须执行自检验证流程（最多循环3次），确保文档完整性：

1. **根文档完整性检查**：逐章检查21章是否完整
2. **模块依赖关系验证**：检查依赖图中显示的依赖关系是否与第7章描述一致
3. **模块文档生成状态**：检查所有模块是否都已生成文档

**循环机制**：
- 发现问题 → 立即处理 → 重新自检验证
- 最多重复3次，确保问题得到解决
- 3次后仍存在的问题记录到最终报告，移交人工更正处理

如果发现有模块未生成文档，会分析原因并由用户决策是否继续生成。

## 参考文档

> 💡 **首次使用建议从 [references/INDEX.md](references/INDEX.md) 开始**，它提供了按用途和检查点的索引。

详细规范和模板请参阅 `references/` 目录：

| 文件 | 用途 |
|------|------|
| [references/INDEX.md](references/INDEX.md) | **入口索引**：按用途/检查点/学习路径分类 |
| [references/quick-ref.md](references/quick-ref.md) | 快速参考（执行预估、场景、策略、命令示例） |
| [references/chapters-ref.md](references/chapters-ref.md) | 章节速查表（21章+11章详情） |
| [references/mermaid-spec.md](references/mermaid-spec.md) | Mermaid 图表规范（含示例代码） |
| [references/doc-format.md](references/doc-format.md) | 文档格式规范（表格、代码块格式、依赖矩阵） |
| [references/self-check-spec.md](references/self-check-spec.md) | 自检验证详细规格（含确认流程、报告格式） |
| [references/project-types-config.md](references/project-types-config.md) | 类型检测 + 文件签名 + 特定章节配置 |
| [references/template.md](references/template.md) | 通用模板 |
| [references/*-template.md](references/) | 各项目类型详细模板（8个） |
| [references/build-guide-*.md](references/) | 各项目类型编译指南（8个） |

## 相关资源

- [SKILL.md](SKILL.md) - 核心 Skill 定义
- [references/](references/) - 配置和模板目录
