# 文档格式规范

本文档定义项目架构文档的格式规范，包括文档头部、表格格式、代码示例等。

## 文档输出结构

| 类型 | 路径 | 命名 |
|------|------|------|
| 根文档 | `docs/` | `{项目名称}项目架构文档.md` |
| 模块文档 | `docs/modules/{模块}/` | `{模块名称}模块架构.md` |

> ⚠️ **重要**：**所有模块的架构文档都必须生成，不可省略！** 即使某些模块功能简单，也必须生成对应的模块架构文档。

**输出文件结构**：
```
{项目根目录}/
└── docs/
    ├── {项目名称}项目架构文档.md    # 根文档（21章）
    └── modules/
        ├── {模块A}/
        │   └── {模块A名称}模块架构.md  # 模块文档（11章）- 必须生成
        ├── {模块B}/
        │   └── {模块B名称}模块架构.md  # 模块文档（11章）- 必须生成
        └── ...                        # 所有模块都必须有对应的文档
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
