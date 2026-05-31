## ADDED Requirements

### Requirement: Trigger log generation based on codegraph complexity metrics
系统 SHALL 使用 codegraph 提供的精确复杂度数据独立判定日志生成需求，与核心模块判定解耦。

#### Scenario: Cognitive complexity above threshold triggers log
- **WHEN** codegraph 可用且某函数的认知复杂度 > 10
- **THEN** 系统 SHALL 将该函数所在模块加入"日志触发模块列表"，并标记该函数为"需生成函数级日志"

#### Scenario: Invocation count above threshold triggers log
- **WHEN** codegraph 可用且某函数的被调用次数 > 3
- **THEN** 系统 SHALL 将该函数所在模块加入"日志触发模块列表"，并标记该函数为"需生成函数级日志"

#### Scenario: Core role node triggers log
- **WHEN** codegraph 可用且某节点 role=core
- **THEN** 系统 SHALL 将该节点所在模块加入"日志触发模块列表"，并标记该节点为"需生成函数级日志"

#### Scenario: Cross-module call chain triggers scenario log
- **WHEN** codegraph 检测到跨模块调用链满足步数 ≥ 6 且参与者 ≥ 4
- **THEN** 系统 SHALL 为该调用链生成场景日志文档

#### Scenario: Module enters log trigger list from multiple sources
- **WHEN** 某模块同时满足多个日志触发条件（例如既是核心模块，又有高复杂度函数，又在关键调用链上）
- **THEN** 系统 SHALL 为该模块生成完整的模块级日志（第一层）+ 各触发函数/场景的独立日志（第二层）

#### Scenario: Module NOT in log trigger list gets no log
- **WHEN** 模块既不是核心模块，也不含认知复杂度>10的函数，也不含被调用次数>3的函数，也不处于关键调用链上
- **THEN** 系统 SHALL NOT 为该模块生成任何日志文档

### Requirement: Produce log trigger report at CP2
系统 SHALL 在 CP2 阶段 codegraph 分析完成后输出"日志触发标记列表"，作为 CP4 日志生成的输入。

#### Scenario: Output log trigger report in codegraph mode
- **WHEN** codegraph 分析管道完成
- **THEN** 系统 SHALL 输出日志触发报告，包含：① 日志触发模块列表（含触发原因：核心模块/codegraph复杂度/codegraph调用链）、② 需生成函数级日志的函数清单（函数名 | 认知复杂度 | 被调用次数 | 所在文件:行号 | 触发原因）、③ 需生成场景日志的调用链清单（场景名 | 步数 | 涉及模块数）

#### Scenario: Output log trigger report in manual mode
- **WHEN** codegraph 不可用
- **THEN** 系统 SHALL 输出日志触发报告，仅包含：核心模块列表（模块级阈值判定）+ 标注"[手动模式] 函数级复杂度无法精确判定，日志可能不完整"

### Requirement: Compute approximate complexity in manual mode
系统 SHALL 在 codegraph 不可用时使用近似方法评估函数复杂度。

#### Scenario: Approximate cognitive complexity via branch counting
- **WHEN** codegraph 不可用且需手动评估函数复杂度
- **THEN** 系统 SHALL 执行 `grep -cE "(if|for|while|case|when|switch)\b"` 统计分支语句数量作为圈复杂度近似值

#### Scenario: Approximate invocation count via reference search
- **WHEN** codegraph 不可用且需手动评估被调用次数
- **THEN** 系统 SHALL 执行 `grep -c "函数名"` 在项目源码中搜索函数引用次数作为近似值

#### Scenario: Manual mode annotation in every log document
- **WHEN** 手动模式下生成任何日志文档
- **THEN** 系统 SHALL 在文档头部标注：`> ⚠️ [手动模式] codegraph 不可用，复杂度判定为近似值，日志覆盖可能不完整。建议安装 @optave/codegraph 重新分析。`
