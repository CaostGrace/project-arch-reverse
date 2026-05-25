# 各语言日志提取指南

本文档定义从各语言源代码中识别和提取日志语句的规则，用于生成核心日志文档（第3章：核心功能日志清单）。

## 提取原则

1. **按调用链扫描**：从核心功能的入口方法开始，沿着调用链逐层扫描所有被调用方法
2. **全量提取**：提取每个方法中所有日志调用语句，不遗漏
3. **标注位置**：每条日志必须标注完整方法名 + 文件路径:行号
4. **保留语义**：记录日志内容的关键部分（如格式化参数），便于排查时搜索

## 各语言日志模式

### Android (Kotlin/Java)

**识别模式**：
```
Log.d(TAG, "message")
Log.i(TAG, "message")
Log.w(TAG, "message")
Log.e(TAG, "message", throwable)
Log.wtf(TAG, "message")
Timber.d/i/w/e("message")           // Timber 库
logger.debug/info/warn/error("msg")  // 第三方日志库
```

**TAG 获取**：从类定义中提取 `private const val TAG = "ClassName"` 或 `private static final String TAG =`

**示例输出**：
| 日志级别 | 平台API调用 | 日志内容摘要 | 所在方法 | 代码位置 |
|----------|------------|-------------|----------|----------|
| INFO | `Log.i(TAG, "sync started, count=%d", n)` | "sync started, count={n}" | `SyncManager.sync()` | `SyncManager.kt:56` |

### iOS (Swift)

**识别模式**：
```
os_log(.debug, "message")
os_log(.info, "message")
os_log(.error, "message")
os_log(.fault, "message")
Logger.debug/info/error/fault("message")
print("message")
NSLog("message")
debugPrint("message")
```

**示例输出**：
| 日志级别 | 平台API调用 | 日志内容摘要 | 所在方法 | 代码位置 |
|----------|------------|-------------|----------|----------|
| INFO | `Logger.info("User logged in: \(userId)")` | "User logged in: {userId}" | `AuthVM.login()` | `AuthVM.swift:45` |

### Flutter (Dart)

**识别模式**：
```
debugPrint("message")
log("message")
logger.d/i/w/e("message")
print("message")
```

### HarmonyOS (ArkTS)

**识别模式**：
```
hilog.debug(domain, TAG, "message")
hilog.info(domain, TAG, "message")
hilog.warn(domain, TAG, "message")
hilog.error(domain, TAG, "message")
console.log/warn/error("message")
```

### Vue.js / React (JavaScript/TypeScript)

**识别模式**：
```
console.log("message")
console.info("message")
console.warn("message")
console.error("message")
console.debug("message")
logger.debug/info/warn/error("message")     // winston/pino 等
```

### Java 后端

**识别模式**：
```
log.debug("message")
log.info("message")
log.warn("message")
log.error("message", exception)
LOGGER.debug/info/warn/error("message")
loggerFactory.getLogger(...).info("message")
```

### Node.js 后端 (JavaScript/TypeScript)

**识别模式**：
```
console.log("message")
console.error("message")
logger.info/warn/error("message")    // winston
log.info/warn/error("message")       // pino
```

### Python

**识别模式**：
```
logging.debug("message")
logging.info("message")
logging.warning("message")
logging.error("message")
logging.exception("message")         // 附带异常堆栈
logger.debug/info/warning/error("message")
print("message")                     // 简单脚本
```

### Go

**识别模式**：
```
log.Debug("message")
log.Info("message")
log.Warn("message")
log.Error("message")
log.Printf("message")
fmt.Println("message")
```

## 日志内容处理规则

### 格式化参数保留
日志中的格式化参数应保留为占位符：
- `Log.i(TAG, "userId=%s, status=%d", userId, status)` → 记录为 `"userId={}, status={}"`
- `log.info("order {} created by {}", orderId, userId)` → 记录为 `"order {} created by {}"`
- `console.log(`[Module] loaded ${count} items`)` → 记录为 `"[Module] loaded {count} items"`

### 异常日志特殊处理
Error 日志若附带异常对象，需记录异常类型：
- `Log.e(TAG, "network error", e)` → 记录为 `"network error" + IOException`
- `log.error("Failed to save", exception)` → 记录为 `"Failed to save" + DataAccessException`

### 日志级别判定
| 日志级别 | 判定依据 | 调试用途 |
|----------|----------|----------|
| DEBUG | 详细追踪变量值、方法调用细节 | 开发环境调试 |
| INFO | 关键业务流程节点、状态变更、定时任务触发 | 追踪用户操作路径 |
| WARN | 可恢复的异常、降级触发、配置缺失但有默认值 | 性能监控 |
| ERROR | 不可恢复的错误、异常捕获、数据不一致 | 线上故障排查 |

## 扫描策略

### 1. 确定扫描范围

```
对每个核心功能:
  1. 找到入口方法（UI 事件处理器 / 后台任务入口）
  2. 分析调用链，收集所有被调用的方法
  3. 对调用链上每个方法，扫描其源码中的日志语句
  4. 记录每条日志的：级别、API、内容、方法名、代码位置
```

### 2. 扫描优先级

1. **ERROR 日志优先**：try-catch 块中的错误日志对排查最关键
2. **入口方法 INFO 日志**：记录功能触发点
3. **分支中的 WARN 日志**：记录异常分支路径
4. **一般 INFO 日志**：记录正常流程节点
5. **DEBUG 日志**：补充记录（可选，大量时可省略）

### 3. 去重规则

- 同方法内相同日志级别和相同模式的消息合并为一条
- 不同调用路径上相同方法的日志分别记录
- try-catch/finally 中的日志独立记录，不与其他分支合并

### 4. 最小日志要求

> ⚠️ 每个核心功能至少提取 3 条关键日志。如果扫描结果少于 3 条，说明该功能的日志覆盖不完整，应在文档中标注"日志覆盖不完整"。

## 输出格式规范

核心日志清单表格（第3章格式）：
```markdown
| 序号 | 日志级别 | 平台API调用 | 日志内容摘要 | 所在方法 | 代码位置 | 含义/触发场景 |
|------|----------|------------|-------------|----------|----------|--------------|
| 1 | INFO | `Log.i(TAG, "...")` | "..." | `Class.method()` | `file.kt:45` | ... |
```
