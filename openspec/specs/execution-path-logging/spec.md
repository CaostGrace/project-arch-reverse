## ADDED Requirements

### Requirement: Trace log statements along execution paths for all log-trigger modules
系统 SHALL 基于 codegraph 调用链追踪日志触发模块列表中所有模块的每条核心能力执行路径上的所有日志语句，包括非核心但含高复杂度函数的模块。

#### Scenario: Extract logs for each step in call chain
- **WHEN** codegraph 可用且核心能力调用链已建立
- **THEN** 系统 SHALL 对调用链上每一步函数 grep 日志语句，输出 §3 执行路径日志追踪表（步骤|方法|日志存在|级别|内容|文件:行号）

#### Scenario: Extract logs for complex function in non-core module
- **WHEN** codegraph 可用且非核心模块中的函数被标记为需生成函数级日志
- **THEN** 系统 SHALL 为该函数建立调用链并逐步骤 grep 日志语句

#### Scenario: Mark missing log steps
- **WHEN** 调用链上某函数无日志语句
- **THEN** 系统 SHALL 标注 `[日志缺失]` 并建议补充

#### Scenario: Calculate log coverage
- **WHEN** 日志追踪表完成
- **THEN** 系统 SHALL 计算日志覆盖率 = 有日志步骤数 / 总步骤数

### Requirement: Analyze error propagation and log patterns
系统 SHALL 分析核心路径上的异常传播链及对应的日志表现。

#### Scenario: Map exceptions to logs
- **WHEN** 调用链上存在 try-catch 或 throws 声明
- **THEN** 系统 SHALL 输出 §4 错误传播表：步骤|方法|可能异常|是否捕获|日志表现|传播方式

#### Scenario: Identify unhandled exception paths
- **WHEN** 调用链上某函数可能抛异常但未被 try-catch 包裹
- **THEN** 系统 SHALL 标注 `[无日志 → 异常将向上冒泡]` 并标记为风险点

### Requirement: Generate code-derived troubleshooting decision tree
系统 SHALL 基于代码逻辑推导故障排查决策树。

#### Scenario: Map failure modes to log patterns
- **WHEN** §3 日志追踪和 §4 错误传播已完成
- **THEN** 系统 SHALL 生成 §8 决策树：日志关键词 → 可能原因(从代码分支推导) → 验证步骤 → 修复建议

#### Scenario: Generate per-function troubleshooting commands
- **WHEN** 决策树生成完成
- **THEN** 系统 SHALL 为每个常见问题生成平台对应的日志过滤命令（grep/adb logcat/xcrun 等）
