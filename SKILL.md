---
name: "project-arch-reverse"
description: "逆向分析项目架构。自动检测项目类型（Android/iOS/Java后端/Vue/React/HarmonyOS Next等），分析模块依赖关系，生成含模块依赖图、数据库设计、页面导航的架构文档。**流程图标注方法名+代码文件:行号**。**强制生成所有模块文档，不可省略**。支持增量更新、人工更正和自检验证（最多3次循环）。Trigger phrases: "分析项目结构", "生成架构文档", "项目架构", "逆向分析", "生成项目文档", "模块架构文档", "架构逆向", "项目分析", "自检架构文档", "检查架构完整性"
---

# 项目架构逆向工程

> TL;DR - 快速执行：检测项目类型 → 扫描模块 → 生成文档 → **自检验证（最多3次循环）** → 确认结果

## 概述

本 skill 对项目架构进行全面的逆向工程，帮助新团队成员快速理解项目结构，使 AI Agent 能够高效分析代码库。

**核心能力**：自动检测项目类型 | 提取模块依赖 | 检测循环依赖 | **流程图标注代码位置（方法名+文件:行号）** | 分析页面导航 | **强制生成所有模块文档** | 增量更新 | **自检验证** | 人工更正

## 输入输出规范

### 输入要求

**必要输入**：
- 工作目录（项目根路径）

**可选输入**：
- 指定分析的模块范围（如 `只分析 app 和 feature:* 模块`）
- 指定项目类型（如果不指定则自动检测）

**用户回复格式示例**：
```json
// 全量分析
{"确认": "全部分析"}

// 指定范围
{"范围": ["app", "feature:foryou", "feature:topic"]}

// 跳过某些模块
{"排除": ["build-logic", "gradle-plugin"]}
```

### 输出结构

**生成的文件结构**：
```
{项目根目录}/
└── docs/
    ├── {项目名称}项目架构文档.md    # 根文档（21章）
    └── modules/
        ├── {模块A}/
        │   └── {模块A名称}模块架构.md  # 模块文档（11章）
        ├── {模块B}/
        │   └── {模块B名称}模块架构.md  # 模块文档（11章）
        └── ...                        # 所有模块文档
```

### 输出文件命名规范

| 文件类型 | 命名格式 | 示例 |
|----------|----------|------|
| 根文档 | `{项目名}项目架构文档.md` | `MyApp项目架构文档.md` |
| Android模块文档 | `{模块名}模块架构.md` | `app模块架构.md` |
| iOS模块文档 | `{Target名}模块架构.md` | `HomeModule模块架构.md` |
| Web模块文档 | `{目录名}模块架构.md` | `store模块架构.md` |

### 依赖矩阵输出格式（内联版）

```markdown
| 模块 | 依赖模块 | 依赖类型 |
|------|----------|----------|
| app | feature:foryou | implementation |
| app | feature:topic | implementation |
| feature:foryou | core:data | implementation |
```

详见完整格式：[references/doc-format.md](references/doc-format.md#依赖矩阵格式)

## 检查点流程

> ⚠️ **必须按顺序执行检查点 0 → 1 → 2 → 2.5（自检循环，最多3次）→ 3**
>
> 详细规范请参阅 `references/` 目录下的对应文件

### 检查点 0：增量检测（必须）

**输入**：工作目录 | **输出**：执行策略 + 检测报告

```
1. 扫描 docs/*.项目架构文档.md → 识别根文档
2. 扫描 docs/modules/**/*模块架构.md → 识别模块文档
3. 扫描项目构建文件，识别所有模块
4. 比对生成策略（A/B/C/D）：
   [A] 全量生成：docs/ + docs/modules/ 均不存在
   [B] 补充生成：根文档存在，模块文档部分缺失
   [C] 增量更新：根文档 + 所有模块文档均存在
   [D] 重建根文档：根文档不存在，模块文档存在
5. 输出：检测报告（存在/缺失列表 + 策略）
```

详见：[references/quick-ref.md](references/quick-ref.md#执行策略选择)

### 检查点 1：项目类型确认（必须）

```
1. 文件签名检测（详见 references/project-types-config.md）
2. 加载配置：references/project-types-config.md
3. 确认范围：全部模块 / 指定模块 → 用户确认
```

详见：[references/project-types-config.md](references/project-types-config.md#项目类型检测配置)

### 项目扫描

```
1. 扫描目录（深度≤5）
2. 解析构建文件 → 提取依赖 → 输出依赖矩阵表格
3. 循环依赖检测
4. 导航分析（nav*.xml / router / App.tsx 等）
5. 生成图表（Mermaid）- ⚠️ 每个步骤标注方法名+代码文件:行号，详见 references/mermaid-spec.md
6. 加载模板：references/{项目类型}-template.md
```

详见：
- [references/quick-ref.md](references/quick-ref.md#模块依赖提取命令) - 依赖提取命令
- [references/doc-format.md](references/doc-format.md#依赖矩阵格式) - 依赖矩阵格式

### 文档生成

```
【A 全量生成】  mkdir docs/ → 生成根文档（21章）→ mkdir docs/modules/{模块}/ × N → ⚠️ 生成所有模块文档
【B 补充生成】  读取现有根文档 → 识别缺失的模块 → ⚠️ 生成所有缺失的模块文档 → 更新根文档
【C 增量更新】  对比代码 vs 文档时间戳 → 只更新变化的模块 → ⚠️ 新模块需生成文档
【D 重建根文档】基于已有模块文档重建根文档 → ⚠️ 检查并补充缺失模块文档
```

> ⚠️ **重要**：**所有模块的架构文档都必须生成，不可省略！**

详见：[references/doc-format.md](references/doc-format.md)

### 检查点 2：结果确认（必须）

```
1. 展示文档（根文档 + 模块文档索引）
2. 收集反馈：用户检查内容
3. 判定结果：
   - 满足需求 → 继续检查点 2.5
   - 需要修改 → 执行修改 → 重新确认
   - 需要人工更正 → 跳过检查点 2.5 → 进入检查点 3
```

详见：[references/self-check-spec.md](references/self-check-spec.md#检查点2结果确认流程)

### 检查点 2.5：自检验证（必须）

> ⚠️ **此步骤必须执行，不可跳过！**

**自检验证循环机制（最多3次）**：
```
自检 → 发现问题 → 立即处理 → 重新自检
                          ↓ (最多3次)
                    记录残留问题 → 继续下一步
```

**自检流程（4个步骤）**：
1. **检查根架构文档完整性** - 21章完整性
2. **检查模块依赖关系正确性** - 依赖图 vs 第7章一致性
3. **检查所有模块文档生成状态** - 已生成/未生成清单+原因
4. **汇总报告并决策**

详见：[references/self-check-spec.md](references/self-check-spec.md)

### 检查点 3：人工更正（可选）

```
1. 展示文档
2. 收集反馈：[章节X] 的 [内容] → [新内容]
3. 执行更正
4. 重复直到满意
```

## 异常处理

| 场景 | 处理 |
|------|------|
| 文件不可读 | 跳过 + 警告 |
| 构建解析失败 | 目录结构推断 |
| 模块过多 (>50) | 分批（≤20/批） |
| 表过多 (>100) | 只主要表（≥3引用） |
| 非标准结构 | 输出已扫描 + 询问 |
| 增量失败 | 备份 + 全量生成 |
| 导航缺失 | 跳过 + 警告 |
| 循环依赖 | 输出矩阵 + 建议 |
| 模块无源码 | 记录 + 报告 + 询问用户 |
| 模块文档生成失败 | 记录 + 继续下一个 + 最终报告 |

## 参考文档

> 💡 **首次使用建议从 [references/INDEX.md](references/INDEX.md) 开始**，它提供了按用途和检查点的索引。

| 文件 | 用途 |
|------|------|
| [references/INDEX.md](references/INDEX.md) | **入口索引**：按用途/检查点/学习路径分类 |
| [references/quick-ref.md](references/quick-ref.md) | 快速参考（执行预估、场景、策略、命令） |
| [references/chapters-ref.md](references/chapters-ref.md) | 章节速查表（21章+11章详情） |
| [references/mermaid-spec.md](references/mermaid-spec.md) | Mermaid 图表规范（含示例代码） |
| [references/doc-format.md](references/doc-format.md) | 文档格式规范（表格、代码块格式、依赖矩阵） |
| [references/self-check-spec.md](references/self-check-spec.md) | 自检验证详细规格（含确认流程） |
| [references/project-types-config.md](references/project-types-config.md) | 类型检测 + 特定章节配置 + 文件签名 |
| [references/template.md](references/template.md) | 通用模板 |
| [references/*-template.md](references/) | 各项目类型详细模板（8个） |
| [references/build-guide-*.md](references/) | 各项目类型编译指南（8个） |

## 资源使用原则

- ✅ 高效使用：按需加载，不预加载所有资源
- ✅ 路径安全：所有文件操作都经过安全校验
- ✅ 错误恢复：失败时自动降级到备选方案
- ✅ 用户确认：关键操作前必须获得用户确认
- ✅ **强制生成：所有模块文档必须生成，不可省略**
- ✅ **自检验证：完成后必须执行自检流程（最多3次循环）**
