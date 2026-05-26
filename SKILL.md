---
name: "project-arch-reverse"
description: "逆向分析项目架构。自动检测项目类型（Android/iOS/Java后端/Vue/React/Flutter/Node.js/HarmonyOS Next等），分析模块依赖关系，生成含模块依赖图、数据库设计、页面导航的架构文档。**流程图标注方法名+代码文件:行号**。**强制生成所有模块文档，不可省略**。支持增量更新、人工更正和自检验证（最多3次循环）。Trigger: 中文\"分析项目结构\" \"生成架构文档\" \"项目架构\" \"逆向分析\" \"生成项目文档\" \"模块架构文档\" \"架构逆向\" \"项目分析\" \"自检架构文档\" \"检查架构完整性\" | English \"analyze project structure\" \"generate architecture docs\" \"reverse engineer project\" \"project architecture analysis\" \"module dependency analysis\""
---

# 项目架构逆向工程

> **一句话**：检测项目 → 扫描模块 → 生成全量架构文档（含代码位置标注）→ 自检验证 → 交付

---

## 场景速查

| 你想做什么 | 说这句话 | 自动策略 | 预计耗时 |
|-----------|---------|---------|---------|
| 首次全量分析 | "帮我分析这个项目" | [A] 全量生成 | 3-10分钟 |
| 更新已有文档 | "项目代码有变化，更新文档" | [C] 增量更新 | 1-5分钟 |
| 补充新模块 | "新增了X模块，补上文档" | [B] 补充生成 | 1-2分钟 |
| 检查完整性 | "帮我检查文档完整性" | [2.5] 自检验证 | 1-3分钟 |
| 只看依赖 | "帮我分析模块依赖" | 跳过文档，只输出依赖矩阵+循环检测 | <1分钟 |

---

## 执行流程

> **必须严格按顺序**：CP0 → CP1 → 扫描 → 生成 → CP2 → CP2.5 → CP3

```
CP0 增量检测 → CP1 类型确认 → 【扫描阶段】 → 【生成阶段】 → CP2 结果确认 → CP2.5 自检验证(≤3次) → CP3 人工更正
```

---

### CP0：增量检测（必须，不可跳过）

**输入**：工作目录 | **输出**：执行策略 + 检测报告 → 等待用户确认

1. 扫描已有文档：
   - `docs/architecture/*.项目架构文档.md` → 根文档
   - `docs/architecture/modules/**/*模块架构.md` → 模块文档
   - `docs/logs/**/*_核心日志.md` → 核心日志文档
2. 扫描项目构建文件，识别所有模块
3. 判定核心模块（满足**任一**即为核心）：

   | 维度 | 条件 | 示例 |
   |------|------|------|
   | 用户可达 | 含UI页面/界面 | app、feature:* |
   | 后台运行 | Service/Worker/定时/推送/同步 | core:sync |
   | 复杂度高 | >20文件 或 >5000行 | 核心业务层 |
   | 用户指定 | 用户明确告知 | 任意模块 |

   > **非核心**：构建脚本、Gradle插件、纯工具库（无UI无后台且复杂度低）、测试模块

4. 选择策略：

   | 策略 | 触发条件 | 动作 |
   |------|---------|------|
   | **A 全量** | docs/architecture/ 不存在 | 创建全部文档 |
   | **B 补充** | 根文档存在，模块文档部分缺失 | 生成缺失模块 |
   | **C 增量** | 根文档+模块文档均存在 | 对比时间戳更新变化部分 |
   | **D 重建** | 根文档不存在，模块文档存在 | 基于模块文档重建根文档 |

   日志文档策略（独立于架构文档）：

   | 策略 | 触发条件 | 动作 |
   |------|---------|------|
   | **L-A** | 核心日志文档均不存在 | 为所有核心模块生成 |
   | **L-B** | 部分核心模块缺少 | 补充缺失模块 |
   | **L-C** | 全部存在 | 对比代码时间戳更新 |

5. ⚠️ **输出检测报告并等待用户确认**（不可跳过）：
   展示：模块清单、已有文档、推荐策略、核心模块列表
   用户可覆盖调整：
   - ✏️ 模块范围：增删模块、排除/包含特定模块
   - ✏️ 核心模块标记：添加/移除核心标记（自动检测仅供参考）
   - ✏️ 策略选择：覆盖推荐策略（如强制全量[A]而非增量[C]）
   - ✏️ 项目类型：覆盖自动检测类型（如将 Android 改为 Flutter）
   
   > 用户指定 > 自动检测，用户确认后才进入 CP1

---

### CP1：项目类型确认（必须）

**输出**：确认的项目类型 + 模块范围 → 进入扫描阶段

1. 文件签名检测（详见 `references/project-types-config.md`）
2. 加载配置：`references/project-types-config.md`
3. 确认范围：全部模块 / 指定模块 → 用户确认

**混合项目处理**：当检测到多种项目类型签名时（如 Android + Flutter 插件、Monorepo 含前后端）：
- 识别主项目类型（最多文件匹配的类型）
- 列出所有检测到的子类型及其模块范围
- 用户确认主类型 + 子类型的模块划分
- 分别使用对应项目类型的模板生成文档

**类型误判回退**：若用户在 CP0/CP1 指定的类型与自动检测结果不一致，**以用户指定为准**，并在扫描阶段使用用户指定类型的配置和模板。

---

### 扫描阶段

**输入**：CP1 输出 | **输出**：依赖矩阵 + Mermaid图 + 模块结构 → 进入生成阶段

| 步骤 | 动作 | 说明 |
|------|------|------|
| 1 | 目录扫描 | 深度≤5 |
| 2 | 构建解析 | 提取依赖 → 输出依赖矩阵表格 |
| 3 | 循环检测 | 识别循环依赖 |
| 4 | 导航分析 | nav*.xml / router / App.tsx 等 |
| 5 | 生成图表 | Mermaid — ⚠️ 每节点标注方法名+文件:行号（见 `references/mermaid-spec.md`） |
| 6 | 加载模板 | 根据项目类型加载 `references/{类型}-template.md`（见参考文档表格） |

**扫描命令速查**：

```bash
# Android        find . -maxdepth 3 -name "build.gradle*" | head -20
# iOS            find . -maxdepth 3 \( -name "*.xcodeproj" -o -name "*.xcworkspace" -o -name "Podfile" \)
# Vue/React      find . -maxdepth 2 \( -name "package.json" -o -name "src" \) | head -5
# Java           find . -maxdepth 2 \( -name "pom.xml" -o -name "build.gradle*" \)
# Flutter        find . -maxdepth 3 -name "pubspec.yaml"
# HarmonyOS      find . -maxdepth 3 \( -name "hvigorfile.ts" -o -name "oh-package.json5" \)
```

详见：`references/quick-ref.md`（依赖提取命令）| `references/doc-format.md`（依赖矩阵格式）

---

### 生成阶段

**输入**：扫描阶段输出 | **输出**：完整架构文档 → 进入 CP2

> ⚠️ **所有模块架构文档必须生成，不可省略！核心模块日志文档也必须生成！**

**按策略执行**：

| 策略 | 执行步骤 |
|------|---------|
| **A** | `mkdir docs/architecture/` → 根文档(21章) → `mkdir docs/architecture/modules/{模块}/` × N → **所有**模块文档(12章) → `mkdir docs/logs/` → **所有**核心模块日志文档(5章) |
| **B** | 读现有根文档 → 识别缺失模块 → 生成缺失模块文档 → 更新根文档 → 补充缺失核心日志 |
| **C** | 对比代码vs文档时间戳 → 更新变化模块 → 新模块生成文档 → 对比更新核心日志 |
| **D** | 基于已有模块文档重建根文档 → 补充缺失模块文档 → 补充缺失核心日志 |

**文档结构**：

```
{项目根}/
└── docs/
    ├── architecture/
    │   ├── {项目名}项目架构文档.md        # 根文档 21章
    │   └── modules/{模块名}/
    │       └── {模块名}模块架构.md         # 模块文档 12章（所有模块必须有）
    └── logs/{模块名}/
        └── {模块名}_核心日志.md            # 日志文档 5章（仅核心模块）
```

**章节速查**（完整定义见 `references/chapters-ref.md`）：

| 文档 | 章数 | 必须标注代码位置的章节（★） |
|------|------|---------------------------|
| 根文档 | 21 | ★第12章业务流程(方法+文件:行号)、第6章依赖图、第13章ER图、第20章模块索引 |
| 模块文档 | 12 | ★第4章业务流程、★第5章数据流、★第6章核心功能流程图、★第11章关键类和方法 |
| 核心日志 | 5 | ★第3章核心功能日志清单、★第5章常见问题排查指南 |

> 💡 **最小可行文档**：如时间紧急，至少完成 P0+P1+P2 章节（见 `references/chapters-ref.md` 优先级表）

**核心功能判定**（核心模块中满足**任一**即为核心功能）：
- 用户直接交互（按钮/表单/导航）
- 后台自主运行（定时同步/推送/预加载）
- 功能复杂度高（复杂计算/多层调用链/自定义协议）

**代码位置标注规范**：流程图/时序图每个节点必须标注 `方法名` + `文件路径:行号`，并附步骤API详解表。

详见：`references/mermaid-spec.md` | `references/doc-format.md` | `references/log-extraction-guide.md`

---

### CP2：结果确认（必须）

1. 展示文档（根文档 + 模块文档索引）
2. 收集反馈：
   - 满足需求 → 继续 CP2.5
   - 需要修改 → 执行修改 → 重新确认
   - 需要人工更正 → **跳过 CP2.5** → 进入 CP3

详见：`references/self-check-spec.md`

---

### CP2.5：自检验证（必须，不可跳过）

**循环机制**：自检 → 发现问题 → 立即处理 → 重新自检（最多3次）→ 残留问题记录后继续

| 步骤 | 检查项 | 发现问题时的修复操作 |
|------|--------|-------------------|
| 1 | 根文档21章完整性 | 缺失→补充；空/占位符→重新扫描填充 |
| 2 | 依赖关系正确性（第6章图 vs 第7章文本） | 不一致→修正+重新生成Mermaid图 |
| 3 | 模块文档生成状态（构建文件识别数 vs 已生成数） | 缺失→立即生成；不可生成→记录原因 |
| 4 | 核心日志文档完整性 | 缺失→调用log-extraction-guide提取；覆盖不足→补充扫描 |
| 5 | 汇总并决策 | 无问题→继续；有处理→重新自检；达3次→记录残留→CP3 |

**终止条件**：无问题 / 用户确认跳过 / 达到3次循环

> 达3次后仍有问题 → 记录为残留问题，移交 CP3（人工更正）处理

详见：`references/self-check-spec.md`

---

### CP3：人工更正（可选）

1. 展示文档
2. 收集反馈：`[章节X] 的 [内容] → [新内容]`
3. 执行更正
4. 重复直到满意

---

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
| 模块无源码 | 记录 + 报告 + 询问 |
| 模块文档生成失败 | 记录 + 继续下一个 + 最终报告 |
| 核心日志提取失败 | 记录 + 标记待处理 + 继续 |
| 日志语句不足 (<3) | 生成文档但标注"日志覆盖不完整" |
| 混合项目类型 | 识别主类型+子类型，用户确认模块划分，分别使用对应模板 |
| Monorepo多包 | 每个包独立识别类型+模块，生成子根文档+统一索引 |
| 用户覆盖类型 | 以用户指定为准，使用指定类型的配置和模板 |
| CP1类型误判 | 用户可在CP0/CP1纠正，确认后使用纠正后的类型 |

## 参考文档

| 文件 | 用途 |
|------|------|
| [references/quick-ref.md](references/quick-ref.md) | 快速参考（执行预估、策略决策树、命令） |
| [references/chapters-ref.md](references/chapters-ref.md) | 章节速查（21+12+5章详情+优先级） |
| [references/mermaid-spec.md](references/mermaid-spec.md) | Mermaid图表规范（含标注三步法+示例） |
| [references/doc-format.md](references/doc-format.md) | 文档格式规范（表格、代码块、依赖矩阵） |
| [references/self-check-spec.md](references/self-check-spec.md) | 自检验证详细规格（含报告格式） |
| [references/project-types-config.md](references/project-types-config.md) | 类型检测 + 文件签名 + 特有分析 |
| [references/log-extraction-guide.md](references/log-extraction-guide.md) | 各语言日志提取指南（含扫描命令） |
| [references/template.md](references/template.md) | 通用模板 |
| [references/android-template.md](references/android-template.md) | Android 项目模板 |
| [references/ios-template.md](references/ios-template.md) | iOS 项目模板 |
| [references/java-backend-template.md](references/java-backend-template.md) | Java 后端项目模板 |
| [references/vue-template.md](references/vue-template.md) | Vue.js 项目模板 |
| [references/react-template.md](references/react-template.md) | React 项目模板 |
| [references/flutter-template.md](references/flutter-template.md) | Flutter 项目模板 |
| [references/nodejs-template.md](references/nodejs-template.md) | Node.js 项目模板 |
| [references/harmonyos-template.md](references/harmonyos-template.md) | HarmonyOS Next 项目模板 |
| [references/build-guide-android.md](references/build-guide-android.md) | Android 编译指南 |
| [references/build-guide-ios.md](references/build-guide-ios.md) | iOS 编译指南 |
| [references/build-guide-java-backend.md](references/build-guide-java-backend.md) | Java 后端编译指南 |
| [references/build-guide-vue.md](references/build-guide-vue.md) | Vue.js 编译指南 |
| [references/build-guide-react.md](references/build-guide-react.md) | React 编译指南 |
| [references/build-guide-flutter.md](references/build-guide-flutter.md) | Flutter 编译指南 |
| [references/build-guide-nodejs.md](references/build-guide-nodejs.md) | Node.js 编译指南 |
| [references/build-guide-harmonyos.md](references/build-guide-harmonyos.md) | HarmonyOS Next 编译指南 |

## 资源使用原则

- ✅ 按需加载，不预加载所有资源
- ✅ 路径安全：所有文件操作经过安全校验
- ✅ 错误恢复：失败时自动降级到备选方案
- ✅ 用户确认：关键操作前必须获得确认
- ✅ **强制生成：所有模块文档必须生成，不可省略**
- ✅ **自检验证：完成后必须执行自检（最多3次循环）**

## 执行完成后自检清单

- [ ] 根文档全部21章？（第12章必须含方法名+文件:行号）
- [ ] 识别出的 N 个模块都生成了模块文档？
- [ ] 模块文档第4/5/6/11章含代码位置标注？
- [ ] 所有核心模块生成了核心日志文档（5章）？
- [ ] Mermaid图表语法正确？（无 `[( )]` 圆柱体，ER图实体名无连字符）
- [ ] 依赖矩阵中每个模块的依赖类型正确标注？
- [ ] 自检循环已执行（至少1次，最多3次）？
- [ ] 如有残留问题，已记录到最终报告？
