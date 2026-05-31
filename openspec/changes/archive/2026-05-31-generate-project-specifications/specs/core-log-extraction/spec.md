## ADDED Requirements

### Requirement: Extract log statements from source code
系统 SHALL 扫描核心模块的源代码，提取所有日志语句并生成核心日志文档。

#### Scenario: Extract Android logs
- **WHEN** 项目类型为 Android
- **THEN** 系统 SHALL 使用 `grep -rn "Log\.[diewv]" --include="*.kt" --include="*.java"` 提取 Log.x 调用

#### Scenario: Extract iOS logs
- **WHEN** 项目类型为 iOS
- **THEN** 系统 SHALL 使用 `grep -rn "os_log\|Logger\.\|NSLog\|print(" --include="*.swift"` 提取日志调用

#### Scenario: Extract Web frontend logs
- **WHEN** 项目类型为 Vue.js 或 React
- **THEN** 系统 SHALL 使用 `grep -rn "console\.\(log\|error\|warn\|info\|debug\)\|logger\."` 提取日志调用

#### Scenario: Extract Java backend logs
- **WHEN** 项目类型为 Java 后端
- **THEN** 系统 SHALL 使用 `grep -rn "log\.\(debug\|info\|warn\|error\)\|LOGGER\."` 提取日志调用

#### Scenario: Extract Flutter logs
- **WHEN** 项目类型为 Flutter
- **THEN** 系统 SHALL 使用 `grep -rn "debugPrint\|log(\|logger\.\|print(" --include="*.dart"` 提取日志调用

#### Scenario: Extract HarmonyOS logs
- **WHEN** 项目类型为 HarmonyOS Next
- **THEN** 系统 SHALL 使用 `grep -rn "hilog\.\|console\." --include="*.ets" --include="*.ts"` 提取日志调用

### Requirement: Generate core log document with 5 chapters for each core module
系统 SHALL 为每个核心模块生成包含 5 章的核心日志文档。

#### Scenario: Generate log doc for core module
- **WHEN** 模块被标记为核心模块
- **THEN** 系统 SHALL 创建 `docs/logs/{模块名}/{模块名}_核心日志.md`，包含全部 5 章

#### Scenario: Skip log doc for non-core module
- **WHEN** 模块被标记为非核心（构建脚本/纯工具库/测试模块，且复杂度低）
- **THEN** 系统 SHALL 不生成核心日志文档

### Requirement: Follow log scan priority order
日志扫描 SHALL 按优先级顺序执行。

#### Scenario: Priority scan order
- **WHEN** 扫描模块日志
- **THEN** 系统 SHALL 按以下优先级扫描：ERROR 日志（try-catch 块）→ 入口方法 INFO 日志 → 分支中 WARN 日志 → 一般 INFO 日志 → DEBUG 日志（可选）

### Requirement: Meet minimum log coverage requirements
每个核心功能 SHALL 覆盖至少 3 种日志场景（功能触发、成功完成、异常捕获、降级回退中的至少 3 种）。

#### Scenario: Core function has adequate log coverage
- **WHEN** 核心功能包含触发日志（INFO）+ 完成日志（INFO）+ 异常日志（ERROR）
- **THEN** 系统 SHALL 标记该功能日志覆盖合格

#### Scenario: Core function has insufficient log coverage
- **WHEN** 核心功能日志语句少于 3 条
- **THEN** 系统 SHALL 生成文档但标注"日志覆盖不完整"

### Requirement: Extract logs along call chains
系统 SHALL 从核心功能的入口方法开始，沿调用链逐层扫描所有被调用方法。

#### Scenario: Extract logs along call chain
- **WHEN** 分析核心功能的日志覆盖
- **THEN** 系统 SHALL 找到入口方法 → 分析调用链 → 收集所有被调用方法 → 扫描每个方法的日志语句

### Requirement: Format log content appropriately
日志内容 SHALL 保留语义并统一格式化参数。

#### Scenario: Format placeholders
- **WHEN** 日志包含格式化参数（如 `Log.i(TAG, "userId=%s, status=%d", userId, status)`）
- **THEN** 系统 SHALL 记录为 `"userId={}, status={}"`，统一使用 `{}` 占位符

#### Scenario: Error log with exception
- **WHEN** ERROR 日志附带异常对象
- **THEN** 系统 SHALL 记录为 `"message" + ExceptionType`，包含异常类型信息

### Requirement: Output log inventory table in standard format
核心日志清单表格 SHALL 包含序号、日志级别、平台API调用、日志内容摘要、所在方法、代码位置、含义/触发场景。

#### Scenario: Generate log inventory table
- **WHEN** 日志提取完成
- **THEN** 系统 SHALL 生成包含 7 列的标准日志清单表格：序号 | 日志级别 | 平台API调用 | 日志内容摘要 | 所在方法 | 代码位置 | 含义/触发场景

### Requirement: Deduplicate repeated log patterns
系统 SHALL 对日志语句进行去重处理。

#### Scenario: Same log in same method
- **WHEN** 同一方法内出现相同日志级别和模式的多次调用
- **THEN** 系统 SHALL 合并为一条记录

#### Scenario: Same log in different call paths
- **WHEN** 同一方法在不同调用路径上被调用，产生相同日志
- **THEN** 系统 SHALL 分别记录每次调用路径的日志

#### Scenario: Log in try-catch/finally block
- **WHEN** 日志出现在 try-catch 或 finally 块中
- **THEN** 系统 SHALL 独立记录，不与其他分支合并
