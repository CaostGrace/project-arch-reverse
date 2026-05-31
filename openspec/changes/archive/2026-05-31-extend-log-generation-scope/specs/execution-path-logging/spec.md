## ADDED Requirements

### Requirement: Trace log statements for non-core module functions triggered by complexity
系统 SHALL 将执行路径日志追踪从核心模块扩展到日志触发模块列表中的所有模块（包括非核心但含高复杂度函数的模块）。

#### Scenario: Trace logs for complex function in non-core module
- **WHEN** codegraph 可用且某非核心模块中的函数被标记为需生成函数级日志（认知复杂度>10 或 被调用次数>3 或 role=core）
- **THEN** 系统 SHALL 为该函数建立调用链并逐步骤 grep 日志语句，输出 §2 执行路径日志追踪表（步骤 | 方法 | 日志存在 | 级别 | 内容 | 文件:行号）

#### Scenario: Mark missing log steps in complex function
- **WHEN** 高复杂度函数的调用链上某步骤无日志语句
- **THEN** 系统 SHALL 标注 `[日志缺失]` 并建议补充

#### Scenario: Calculate log coverage for complex function
- **WHEN** 高复杂度函数的日志追踪表完成
- **THEN** 系统 SHALL 计算日志覆盖率 = 有日志步骤数 / 总步骤数 × 100%

### Requirement: Extract cross-module end-to-end log chain for scenario logs
系统 SHALL 为场景日志文档提取跨模块端到端日志链。

#### Scenario: End-to-end log trace for scenario
- **WHEN** 场景涉及多个模块的调用链
- **THEN** 系统 SHALL 对每条调用链上的每个模块步骤 grep 日志语句，在场景日志 §2 中输出完整的跨模块日志追踪表

#### Scenario: Cross-module log join point identification
- **WHEN** 跨模块调用链日志提取完成
- **THEN** 系统 SHALL 识别并标注模块间日志连接点：上游模块最后一条日志对应下游模块第一条日志，输出在场景日志 §3

#### Scenario: Cross-module exception propagation mapping
- **WHEN** 跨模块调用链涉及异常传播
- **THEN** 系统 SHALL 在场景日志 §4 中输出异常传播全景图：每个传播节点的日志表现 → 模块边界 → 捕获/重新抛出/转换
