## MODIFIED Requirements

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

#### Scenario: Extract logs for log-trigger module regardless of core status
- **WHEN** 模块在日志触发模块列表中（无论是否为核心模块）
- **THEN** 系统 SHALL 扫描该模块的源代码提取日志语句

### Requirement: Generate core log document with 5 chapters for each core module
系统 SHALL 为日志触发模块列表中的每个模块生成包含 8 章的模块级核心日志文档（codegraph 模式）或 5 章（手动模式）。

#### Scenario: Generate log doc for log-trigger module
- **WHEN** 模块在日志触发模块列表中
- **THEN** 系统 SHALL 创建 `docs/logs/{模块名}/{模块名}_核心日志.md`，codegraph 模式下包含全部 8 章，手动模式下包含全部 5 章

#### Scenario: Skip log doc for non-trigger module
- **WHEN** 模块不在日志触发模块列表中（构建脚本/纯工具库/测试模块，且无高复杂度函数，不在关键调用链上）
- **THEN** 系统 SHALL 不生成任何日志文档

#### Scenario: Log doc header includes trigger reason annotation
- **WHEN** 为模块生成核心日志文档
- **THEN** 系统 SHALL 在文档头部标注该模块的日志触发原因：`[核心模块]`、`[codegraph: 复杂函数触发]`、`[codegraph: 调用链触发]`（可多项）

## REMOVED Requirements

### Requirement: Skip log doc for non-core module
**Reason**: 日志生成触发条件已从"核心模块"扩展为"日志触发模块列表"（核心模块 ∪ codegraph 高复杂度函数模块 ∪ 关键调用链模块），非核心模块如果包含高复杂度函数仍然需要生成日志文档。
**Migration**: 原"非核心模块不生成日志"的逻辑替换为"不在日志触发模块列表中则不生成日志"。非核心但有高复杂度函数的模块现在会自动触发日志生成。
