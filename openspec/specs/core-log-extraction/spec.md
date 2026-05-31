## ADDED Requirements

### Requirement: Extract log statements from source code
系统 SHALL 扫描日志触发模块列表的源代码，提取所有日志语句并生成日志文档。

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

### Requirement: Generate log documents for all log-trigger modules
系统 SHALL 为日志触发模块列表中的每个模块生成日志文档。日志触发模块 = 核心模块 ∪ codegraph 高复杂度函数所在模块 ∪ codegraph 关键调用链模块。

#### Scenario: Generate log doc for any log-trigger module
- **WHEN** 模块在日志触发模块列表中
- **THEN** 系统 SHALL 创建 `docs/logs/{模块名}/{模块名}_核心日志.md`，codegraph 模式下包含全部 8 章，手动模式下包含全部 5 章，文档头部标注触发原因

#### Scenario: Generate function-level log for high-complexity functions
- **WHEN** codegraph 可用且函数被标记为需生成函数级日志（认知复杂度>10 或 被调用次数>3 或 role=core）
- **THEN** 系统 SHALL 创建 `docs/logs/{所属模块}/{函数名}_关键函数日志.md`，包含 5 章

#### Scenario: Generate scenario log for cross-module call chains
- **WHEN** codegraph 可用且跨模块调用链被标记为需生成场景日志（步数≥6, 参与者≥4）
- **THEN** 系统 SHALL 创建 `docs/logs/{主要模块}/{场景名}_场景日志.md`，包含 5 章

#### Scenario: Skip log docs for non-trigger modules
- **WHEN** 模块不在日志触发模块列表中（构建脚本/纯工具库/测试模块，且无高复杂度函数，不在关键调用链上）
- **THEN** 系统 SHALL 不生成任何日志文档

#### Scenario: Manual mode annotation in log headers
- **WHEN** codegraph 不可用
- **THEN** 系统 SHALL 在所有生成的日志文档头部标注：`> ⚠️ [手动模式] codegraph 不可用，复杂度判定为近似值，日志覆盖可能不完整。建议安装 @optave/codegraph 重新分析。`

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

### Requirement: Include severity alert in log coverage evaluation (§5)
日志覆盖评估 §5 SHALL 包含严重度告警框。

#### Scenario: CRITICAL severity alert
- **WHEN** 调用链覆盖率为 0% 或入口无日志或异常处理无日志
- **THEN** 系统 SHALL 在 §5 顶部输出 🚨 🔴P0 严重告警框，包含判定原因和受影响步骤数

#### Scenario: WARNING severity alert
- **WHEN** 调用链出口无日志或覆盖率 < 50%
- **THEN** 系统 SHALL 在 §5 顶部输出 ⚠️ 🟡P1 警告告警框

#### Scenario: OK severity alert
- **WHEN** 调用链覆盖率 ≥ 50% 且有 1-2 步缺失
- **THEN** 系统 SHALL 在 §5 顶部输出 💡 🟢P2 合格提示

#### Scenario: Recommendation table below alert
- **WHEN** 严重度为 🔴P0 或 🟡P1
- **THEN** 系统 SHALL 在告警框下方附加 5 列建议埋点表：优先级 | 代码位置 | 建议日志内容 | 建议级别 | 触发原因

### Requirement: Include log output examples and grep commands in log inventory (§6)
日志清单 §6 SHALL 包含各平台实际输出示例和可直接使用的 grep 命令。

#### Scenario: Log table with output example column
- **WHEN** 生成 §6 日志清单表格
- **THEN** 系统 SHALL 增加"实际输出示例"列，根据项目类型生成对应平台的日志输出格式

#### Scenario: Log table with grep command column
- **WHEN** 生成 §6 日志清单表格
- **THEN** 系统 SHALL 增加"grep命令"列，生成平台对应的过滤命令

### Requirement: Include platform log format reference in §7
日志级别说明与排查过滤 §7 SHALL 包含项目类型对应的日志格式速查。

#### Scenario: Platform format reference
- **WHEN** 生成 §7
- **THEN** 系统 SHALL 包含"本平台日志格式"小节：日志行格式模板、各字段含义、默认过滤命令

### Requirement: Enhance troubleshooting decision tree with expected output (§8)
故障排查决策树 §8 的每个排查步骤 SHALL 包含 4 要素。

#### Scenario: Troubleshooting step with 4 elements
- **WHEN** 生成 §8 决策树每个排查步骤
- **THEN** 系统 SHALL 包含：① 排查命令（可复制执行）② 预期正常输出示例 ③ 预期异常输出示例 ④ 无输出时的诊断方向
