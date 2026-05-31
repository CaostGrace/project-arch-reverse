# 文档格式规范

本文档定义项目架构文档的格式规范，包括文档头部、表格格式、代码示例等。

## 分析模式标注

> 根文档第 1 章（修订历史）需标注分析引擎类型。

```markdown
| 版本 | 修订人 | 修订时间 | 修订内容 | 分析引擎 |
|------|--------|----------|----------|----------|
| v1.0 | AI Agent | 2026-05-31 | 初始版本 | codegraph |
```

| 分析引擎 | 标注 | 说明 |
|----------|------|------|
| codegraph | `codegraph` | 使用 @optave/codegraph 作为分析引擎 |
| 手动 | `manual` | codegraph 不可用，使用手动 grep 扫描 |

## codegraph 核心能力档案格式 ★

> codegraph 分析模式下，每个 role=core 的节点必须生成完整档案，存放在模块文档 §6 下。

### 档案模板

```markdown
### 核心能力: <函数名>

**用途**: <基于函数名+代码推断的功能描述>[需人工确认]
**所属模块**: <模块名>
**触发类型**: UI触发 / 系统触发 / 外部事件
**复杂度**: 认知 X | 圈 Y | 可维护性 Z
**代码位置**: <文件路径:行号>

**调用者**（谁调用了它）:
| 调用方 | 触发类型 | 代码位置 |
|--------|---------|----------|
| OrderActivity.submit() | UI触发 | OrderActivity.kt:56 |

**被调用者**（它调用了谁）:
| 被调用函数 | 所在模块 | 代码位置 |
|-----------|---------|----------|
| validateStock() | order | OrderValidator.kt:12 |

**触发场景**:
- <场景1>: <触发条件 + 来源>
- <场景2>: <触发条件 + 来源>

**时序图** (≥6步, ≥4参与者, 从入口到核心到终点):
` ` `mermaid
sequenceDiagram
    ...
` ` `

**业务流程图** (分支逻辑):
` ` `mermaid
flowchart TD
    ...
` ` `
```

### 重构建议清单格式

```markdown
| 优先级 | 问题类型 | 位置 | 当前值 | 阈值 | 建议 |
|--------|---------|------|--------|------|------|
| P0 | 循环依赖 | A→B→C→A | 3节点 | 0 | 提取公共接口到 core 模块 |
| P1 | 高复杂度 | OrderService.create() | cognitive 22 | 15 | 拆分为 validate/calculate/save |
| P1 | 高耦合 | PaymentGateway | fan-out 14 | 10 | 引入 Facade 模式解耦 |
| P2 | 边界违规 | data→ui | 直接调用 | 禁止 | 通过 domain 层中转 |
```

### 死代码清单格式

```markdown
| 函数/类 | 文件:行号 | 角色 | 复杂度 | 建议 |
|---------|----------|------|--------|------|
| LegacyService.process() | legacy/LegacyService.java:34 | dead | 8 | 确认后删除 |

**统计**: 死函数 X/总数 Y = Z%
```

## 日志分析文档格式 ★

### 执行路径日志追踪表 (§3)

```markdown
### <核心能力名> — 执行路径日志追踪

| 步骤 | 方法 | 日志存在 | 级别 | 日志内容 | 文件:行号 |
|------|------|:---:|------|---------|----------|
| 1 | submit() | ✅ | INFO | "Order submitted" | OrderActivity.kt:56 |
| 2 | createOrder() | ✅ | DEBUG | "Creating: {id}" | OrderVM.kt:34 |
| 3 | validateStock() | ❌ | — | [日志缺失] | Validator.kt:12 |

**日志覆盖率**: X/Y = Z%
```

### 错误传播表 (§4)

```markdown
### <核心能力名> — 错误传播分析

| 步骤 | 方法 | 可能异常 | 捕获? | 日志表现 | 传播方式 |
|------|------|---------|:---:|---------|---------|
| 3 | validate() | StockNotFound | ✅ | [WARN] "Stock: {SKU}" | return false |
| 4 | calculate() | ArithmeticEx | ❌ | [无日志] | throws↑ |
| 5 | saveToDB() | SQLException | ✅ | [ERROR] "DB: {msg}" | wrap→OrderEx |
```

### 日志覆盖评估 (§5)

```markdown
### 日志覆盖评估

| 模块 | 核心函数数 | 有日志 | 缺失 | 覆盖率 |
|------|----------|--------|------|:---:|
| order | 12 | 9 | 3 | 75% |

**缺失日志的关键函数**:
| 函数 | 文件:行号 | 原因 | 建议 |
|------|----------|------|------|
| validateStock() | Validator.kt:12 | 无日志 | 添加 INFO 日志记录校验结果 |
```

### 故障排查决策树 (§8)

```markdown
### 故障: <日志关键词+代码位置>

**可能原因 1**: <从代码分支推导>
- 验证: <grep 命令 / 检查项>
- 修复: <具体方案>

**可能原因 2**: ...
```

## 格式自检清单

生成文档后，快速逐项检查以下格式问题：

- [ ] Mermaid ER图实体名无连字符（`-`），用下划线代替
- [ ] Mermaid 流程图节点无 `[( )]` 圆柱体形状（与括号冲突）
- [ ] Mermaid subgraph 标识符不含特殊字符
- [ ] Mermaid flowchart 中所有箭头引用的节点 ID 都已在前面定义（未定义节点会导致解析错误 `got 'PS'`）
- [ ] Mermaid 节点文本含 `()` 时必须用双引号包裹（如 `AUTH["认证<br>login()"]`），否则 `()` 被当作圆角矩形语法导致 `got 'PS'` 错误
- [ ] Mermaid 节点文本首尾无裸括号 `()`，避免被误解析为圆角矩形语法
- [ ] Mermaid 节点文本中括号必须成对匹配，末尾不能有多余的 `)`
- [ ] Mermaid 时序图消息箭头遵循分层架构：UI→Service→API，不跳层直接调用
- [ ] 表格对齐标记正确：`|:---|` 左对齐、`|---:|` 右对齐、`|:---:|` 居中
- [ ] 代码块标注了正确的语言标识（`kotlin`/`swift`/`typescript` 等）
- [ ] 所有流程图节点标注了 `方法名` + `文件路径:行号`
- [ ] 所有图表（依赖图、流程图、时序图、导航图、类图、ER图）中涉及代码的节点/参与者都标注了代码位置
- [ ] 每个核心功能/能力都有独立的时序图（不允许合并多个功能）
- [ ] 每个时序图满足详细度要求：≥6步调用、≥4参与者、完整分层（不跳层）、有返回消息
- [ ] 步骤API详解表包含：步骤编号、步骤名称、核心方法、输入参数、返回结果、代码位置、说明
- [ ] 所有关键代码步骤都在文档中展示了对应的代码片段（关键代码示例为必需，非推荐）
- [ ] ER图下方附带表结构说明表，标注每个表的代码位置（建表语句/迁移文件/实体类路径:行号）
- [ ] 类图下方附带类说明表，标注每个类的文件路径和行号
- [ ] 依赖矩阵的"依赖类型"字段使用标准值（implementation/api/compileOnly 等）
- [ ] 数据库表结构含约束、外键、索引列
- [ ] 代码位置标注格式一致：同一文档内使用统一的 `文件相对路径:行号` 格式（如 `app/src/MainActivity.kt:45`），不混用绝对路径和相对路径

## 文档输出结构

| 类型 | 路径 | 命名 |
|------|------|------|
| 根文档 | `docs/architecture/` | `{项目名称}项目架构文档.md` |
| 模块文档 | `docs/architecture/modules/{模块}/` | `{模块名称}模块架构.md` |
| 核心日志文档 | `docs/logs/{模块}/` | `{模块名称}_核心日志.md` |
| 函数级日志 | `docs/logs/{模块}/` | `{函数名}_关键函数日志.md` |
| 场景日志 | `docs/logs/{模块}/` | `{场景名}_场景日志.md` |

> ⚠️ **重要**：**所有模块的架构文档都必须生成，不可省略！** 即使某些模块功能简单，也必须生成对应的模块架构文档。日志触发模块列表中的模块必须生成日志文档。非触发模块（构建脚本、纯工具库、测试模块，且无高复杂度函数、不在关键调用链上）无需日志文档。

**输出文件结构**：
```
{项目根目录}/
└── docs/
    ├── architecture/                          # 架构相关文档
    │   ├── {项目名称}项目架构文档.md           # 根文档（21章）
    │   └── modules/
    │       ├── {模块A}/
    │       │   └── {模块A名称}模块架构.md       # 模块文档（12章）- 必须生成
    │       └── ...                             # 所有模块都必须有对应的架构文档
    └── logs/                                   # 日志相关文档
        ├── {模块A}/
        │   ├── {模块A名称}_核心日志.md          # 第一层: 模块级日志（8/5章）
        │   ├── {函数名}_关键函数日志.md         # 第二层: 高复杂度函数日志
        │   └── {场景名}_场景日志.md             # 第二层: 核心场景日志
        └── ...                                 # 仅日志触发模块有日志目录
```

## 根文档头部

```markdown
# {项目名称} 项目架构文档

## 1. 文档修订历史

| 版本 | 修订人 | 修订时间 | 修订内容 |
|------|--------|----------|----------|
| v1.0 | - | {日期} | 初始版本 |
```

## 模块文档头部

```markdown
# {模块名称} 模块架构

## 1. 模块概述

- **模块名称**：{模块名}
- **所在层级**：{App/Feature/Core层}
- **核心职责**：{一句话描述}
```

## 依赖表格

```markdown
| 依赖模块 | 说明 |
|----------|------|
| core:data | 数据访问层 |
| core:domain | 业务逻辑层 |
```

## 路由表格

```markdown
| 路由 | 参数 | 说明 |
|------|------|------|
| `forYou` | 无 | 为你推荐首页 |
| `topic/{topicId}` | topicId | 话题详情 |
```

## 导航调用示例

### Kotlin (Android)
```kotlin
// 导航到详情页
navController.navigate("topic/${topicId}")

// 接收路由参数
val topicId: String = checkNotNull(savedStateHandle["topicId"])
```

### Swift (iOS)
```swift
// 导航到详情页
navigationController?.pushViewController(detailVC, animated: true)

// 接收路由参数
let topicId = navigationContext.params["topicId"]
```

### Vue.js
```javascript
// 导航到详情页
this.$router.push({ name: 'topic', params: { topicId: '123' }})

// 接收路由参数
const topicId = this.$route.params.topicId
```

### React
```tsx
// 导航到详情页
import { useNavigate } from 'react-router-dom'
const navigate = useNavigate()
navigate(`/topic/${topicId}`)

// 接收路由参数
import { useParams } from 'react-router-dom'
const { topicId } = useParams()
```

## 依赖矩阵格式

### 模块依赖矩阵输出格式

| 模块 | 依赖模块 | 依赖类型 |
|------|----------|----------|
| app | feature:foryou | implementation |
| app | feature:topic | implementation |
| feature:foryou | core:data | implementation |
| feature:topic | core:domain | implementation |

### 依赖类型说明

| 类型 | 说明 | 使用场景 |
|------|------|----------|
| implementation | 实现依赖 | Android/Vue/React/Flutter |
| compileOnly | 编译时依赖 | Gradle/Java |
| runtimeOnly | 运行时依赖 | Gradle/Java |
| api | API 暴露依赖 | Android/Gradle |
| devDependencies | 开发依赖 | Node.js/Web |
| peerDependencies | 同级依赖 | Node.js/Web |
| embed | 嵌入依赖 | Flutter |
| path | 本地路径依赖 | Flutter/React Native |

## 数据库表格格式

```markdown
| 表名 | 字段名 | 类型 | 约束 | 外键 | 索引 | 说明 |
|------|--------|------|------|------|------|------|
| news_resource | id | INTEGER | PRIMARY KEY AUTOINCREMENT | - | ✅ | 主键 |
| news_resource | topic_id | INTEGER | NOT NULL | topic(id) | ✅ | 关联话题 |
```

### 字段类型说明

| 类型 | 说明 |
|------|------|
| INTEGER | 整数类型 |
| TEXT | 文本类型 |
| REAL | 浮点数类型 |
| BLOB | 二进制类型 |
| VARCHAR(n) | 变长字符类型 |
| DATETIME | 日期时间类型 |

### 约束说明

| 约束 | 说明 |
|------|------|
| PRIMARY KEY | 主键 |
| NOT NULL | 非空 |
| UNIQUE | 唯一 |
| AUTOINCREMENT | 自增 |
| DEFAULT | 默认值 |

## 外键关联关系

```markdown
| 父表 | 父字段 | 子表 | 子字段 | 关系类型 | 级联操作 |
|------|--------|------|--------|----------|----------|
| topic | id | news_resource | topic_id | 一对多 | CASCADE DELETE |
```

### 关系类型

| 关系类型 | 说明 |
|----------|------|
| 一对一 | 一个记录对应另一个表中的一条记录 |
| 一对多 | 一个记录对应另一个表中的多条记录 |
| 多对多 | 两个表中多个记录相互对应 |

### 级联操作

| 操作 | 说明 |
|------|------|
| CASCADE | 删除/更新父表记录时，子表记录同步删除/更新 |
| SET NULL | 删除/更新父表记录时，子表外键设为 NULL |
| RESTRICT | 阻止删除/更新父表记录 |
| NO ACTION | 不做任何操作 |

## 索引设计

```markdown
| 表名 | 索引名 | 字段 | 类型 | 说明 |
|------|--------|------|------|------|
| news_resource | idx_topic_id | topic_id | 普通索引 | 加速话题查询 |
| user | uk_email | email | 唯一索引 | 邮箱唯一约束 |
```

### 索引类型

| 类型 | 说明 |
|------|------|
| 普通索引 | 最基本的索引，可重复 |
| 唯一索引 | 索引值唯一，不可重复 |
| 主键索引 | 主键自动创建，特殊唯一索引 |
| 全文索引 | 文本内容搜索 |

## 表格格式规范

### 表格对齐
- 使用 `|:---|` 左对齐
- 使用 `|---:|` 右对齐
- 使用 `|:---:|` 居中对齐

### 表格换行
- 使用 `<br>` 进行单元格内换行
- 示例：`| 字段1<br>字段2 | 说明 |`

### 表格省略
- 使用 `-` 表示空值
- 示例：`| - | 无外键 |`

## 代码块规范

### 语言标识

| 语言 | 标识 |
|------|------|
| Kotlin | `kotlin` |
| Java | `java` |
| Swift | `swift` |
| Objective-C | `objc` |
| JavaScript | `javascript` |
| TypeScript | `typescript` |
| Python | `python` |
| Go | `go` |
| SQL | `sql` |
| YAML | `yaml` |
| JSON | `json` |
| XML | `xml` |
| Bash | `bash` |
| Markdown | `markdown` |

### 代码注释规范
- Kotlin/Java: `// 单行注释` 或 `/* 多行注释 */`
- Swift: `// 单行注释` 或 `/* 多行注释 */`
- JavaScript/TypeScript: `// 单行注释` 或 `/* 多行注释 */`
- Python: `# 单行注释` 或 `""" 多行注释 """`

## 根文档第12章聚合展示格式 ★

> 根文档第 12 章采用全量聚合模式，汇总所有模块的核心功能，包含跨模块分析。

### §12.1 整体业务概览图格式

```markdown
## 12. 主要业务流程图

### 12.1 整体业务概览

> 以下为跨模块整体业务概览图，详细流程见各模块架构文档。

` ` `mermaid
flowchart TD
    A["用户入口<br>详见 app 模块"] --> B["核心业务<br>详见 business 模块"]
    B --> C["数据持久化<br>详见 data 模块"]
` ` `
```

### §12.2 核心功能索引表格式（7 列）

```markdown
### 12.2 核心功能索引

| 功能ID | 功能名 | 所属模块 | 类型 | 根文档节号 | 来源模块文档 | 说明 |
|--------|--------|----------|------|-----------|-------------|------|
| F-001 | 用户登录 | auth | 模块 | §12.3.1.1 | [auth模块架构.md §6.1](modules/auth/auth模块架构.md) | 用户名密码登录 |
| F-002 | 令牌刷新 | auth | 模块 | §12.3.1.2 | [auth模块架构.md §6.2](modules/auth/auth模块架构.md) | 自动续期 |
| F-003 | 下单流程 | 跨模块 | 综合 | §12.4.1 | [order模块 §6](modules/order/) + [payment模块 §6](modules/payment/) | app→order→payment 端到端 |
```

### §12.3 模块级核心功能汇总格式

```markdown
### 12.3 模块级核心功能

#### 12.3.1 auth 模块 — 核心功能

##### 12.3.1.1 用户登录
> 来源：[auth模块架构.md §6.1 用户登录](modules/auth/auth模块架构.md)

[独立时序图 + 步骤详解表 + 代码片段]

##### 12.3.1.2 令牌刷新
> 来源：[auth模块架构.md §6.2 令牌刷新](modules/auth/auth模块架构.md)

[独立时序图 + 步骤详解表 + 代码片段]

#### 12.3.2 order 模块 — 核心功能
...
```

### §12.4 跨模块综合功能分析格式

```markdown
### 12.4 跨模块综合功能

#### 12.4.1 完整下单流程
> 端到端流程：app → order → payment → inventory。
> 详细时序图规范见 `references/mermaid-spec.md`「跨模块时序图」章节。

[跨模块时序图 + 9列步骤详解表 + 各模块关键代码片段]
```

## 核心日志文档格式

### 日志文档头部

```markdown
# {模块名称} 核心日志

## 1. 文档修订历史

| 版本 | 修订人 | 修订时间 | 修订内容 |
|------|--------|----------|----------|
| v1.0 | - | {日期} | 初始版本，基于代码扫描自动提取 |
```

### 日志清单表格格式

```markdown
| 序号 | 日志级别 | 平台API调用 | 日志内容摘要 | 所在方法 | 代码位置 | 含义/触发场景 |
|------|----------|------------|-------------|----------|----------|--------------|
| 1 | INFO | `Log.i(TAG, "msg")` | "User login started" | `LoginVM.login()` | `LoginVM.kt:45` | 用户点击登录按钮 |
```

### 各平台日志API参考

| 平台 | TAG/标识 | 典型API调用模式 |
|------|----------|----------------|
| Android | 类名前缀 `TAG = "ClassName"` | `Log.d/i/w/e(TAG, "msg")` |
| iOS | subsystem + category | `os_log(.debug, "msg")` / `Logger.info("msg")` |
| Flutter | 类名前缀 | `debugPrint()` / `log()` / `logger.i()` |
| HarmonyOS | domain (0x0000-0xFFFF) | `hilog.info(domain, TAG, "msg")` |
| Web (JS/TS) | 模块/组件名 | `console.log/warn/error("[Module]", msg)` |
| Java 后端 | Logger 实例名 | `log.info/warn/error("msg")` / `LOGGER.debug()` |
```

### 函数级日志文档格式 ★

**文档头部**：
```markdown
# {函数名} 关键函数日志

> 触发原因: [认知复杂度: 42] [被调用次数: 15] [codegraph role=core]
> 所属模块: {模块名}
> 代码位置: `path/to/file.kt:120-180`
```

**§1 函数概述**：
```markdown
## 1. 函数概述

| 属性 | 说明 |
|------|------|
| **用途** | 基于函数名语义 + 代码推断的功能描述 |
| **签名** | `fun submitOrder(params: OrderParams): Result<Order>` |
| **复杂度** | 认知 42 | 圈 18 |
| **代码位置** | OrderService.kt:120-180 |
| **主要调用者** | OrderActivity.submit() (UI触发), OrderScheduler.process() (系统触发) |
```

**§2 执行路径日志追踪**：
```markdown
## 2. 执行路径日志追踪

| 步骤 | 方法 | 日志存在 | 级别 | 日志内容 | 文件:行号 |
|------|------|:---:|------|---------|----------|
| 1 | submit() | ✅ | INFO | "Order submitted" | OrderActivity.kt:56 |
| 2 | createOrder() | ✅ | DEBUG | "Creating: {id}" | OrderVM.kt:34 |
| 3 | validateStock() | ❌ | — | [日志缺失] | Validator.kt:12 |
```

**§3 异常传播路径**：
```markdown
## 3. 异常传播路径

| 步骤 | 方法 | 可能异常 | 捕获? | 日志表现 | 传播方式 |
|------|------|---------|:---:|---------|---------|
| 3 | validate() | StockNotFound | ✅ | [WARN] "Stock: {SKU}" | return false |
| 5 | saveToDB() | SQLException | ✅ | [ERROR] "DB: {msg}" | wrap→OrderEx |
```

**§4 日志覆盖评估**：
```markdown
## 4. 日志覆盖评估

**覆盖率**: 8/12 = 67%

**缺失日志步骤**: validateStock() → 建议添加 INFO 日志记录校验结果
```

**§5 故障排查指南**：
```markdown
## 5. 故障排查指南

` ` `mermaid
flowchart TD
    A["[ERROR] DB: {msg}"] --> B["检查数据库连接"]
    A --> C["检查 SQL 语法"]
    B --> D["验证 DataSource 配置"]
    C --> E["review 最近迁移文件"]
` ` `

**日志过滤命令**:
```bash
adb logcat | grep "OrderService" | grep -E "ERROR|WARN"
```
```

### 场景日志文档格式 ★

**文档头部**：
```markdown
# {场景名} 场景日志

> 触发原因: [跨模块调用链: 6模块, 12步] [核心业务流程]
> 涉及模块: UI → Service → RiskEngine → Gateway → Payment → DB
```

**§2 端到端日志追踪**：
```markdown
## 2. 端到端日志追踪

| 模块 | 步骤 | 方法 | 日志存在 | 级别 | 日志内容 | 文件:行号 |
|------|------|------|:---:|------|---------|----------|
| app | 1 | submitOrder() | ✅ | INFO | "Order submitted" | OrderActivity.kt:56 |
| order | 2 | createOrder() | ✅ | INFO | "Creating: {id}" | OrderService.kt:34 |
| risk | 3 | evaluateRisk() | ❌ | — | [日志缺失] | RiskEngine.kt:89 |
| ...
```

**§3 各模块日志连接点**：
```markdown
## 3. 各模块日志连接点

| 上游模块 | 上游最后一条日志 | 下游模块 | 下游第一条日志 | 关联说明 |
|----------|----------------|---------|---------------|---------|
| app | [INFO] "Order submitted" @ OrderActivity.kt:56 | order | [INFO] "Creating: {id}" @ OrderService.kt:34 | 用户提交→订单创建 |
| order | [INFO] "Order saved" @ OrderService.kt:78 | risk | [无日志] @ RiskEngine.kt:89 | ⚠️ 日志链断裂 |
```

## 任务清单文件格式 (.task-checklist.md)

> 生成阶段使用的持久化进度追踪文件，存储于 `docs/architecture/.task-checklist.md`。

### 文件头部

```markdown
# 架构文档生成任务清单

**项目**: <项目名>    **类型**: <类型>
**策略**: <A/B/C/D>   **创建**: <ISO时间戳>
**进度**: <完成数>/<总数> (<跳过数> 跳过)  **状态**: 进行中/已完成
```

### 模块章节任务格式

```markdown
### Layer 0 — 无依赖模块

#### <模块名> 模块 [▰▰░░░░░░░░ 25%]
- [x] §1 模块概述 (P1)
- [x] §2 UI组件清单 (P2)
- [>] §3 依赖关系 (P1)
- [ ] §4 ★业务流程 (P0)
- [ ] §5 ★数据流 (P0)
- [ ] §6 ★核心功能流程图 (P0)
- [ ] §7 数据依赖关系 (P2)
- [ ] §8 依赖场景说明 (P2)
- [ ] §9 页面与路由 (P2)
- [ ] §10 导航调用示例 (P2)
- [ ] §11 ★关键类和方法 (P1)
- [ ] §12 测试策略 (P2)
```

### 状态标记说明

| 标记 | 含义 | 说明 |
|------|------|------|
| `[ ]` | 未开始 | 初始状态 |
| `[>]` | 进行中 | 正在生成，中断恢复时重置为 `[ ]` |
| `[1]` | 失败待重试 | 首次失败，将自动重试一次 |
| `[x]` | 完成 | 生成成功 |
| `[!]` | 跳过 | 重试后仍失败，记录原因 |

### 进度条格式

```
[▰▰▰░░░░░░░░ 25%]     — 使用 Unicode block 字符 ▰(full) + ░(empty)
```

### 跳过清单格式

```markdown
## 跳过清单
- [!] <模块> §<编号>: <错误类型> @ <代码位置>
- [!] data模块 §4: NullPointerException @ DataParser.java:23
```
