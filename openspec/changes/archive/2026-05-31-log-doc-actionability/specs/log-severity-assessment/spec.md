## ADDED Requirements

### Requirement: Assess log coverage severity for each call chain
系统 SHALL 在 codegraph 分析管道中对每条调用链执行日志覆盖严重度判定。

#### Scenario: Severity CRITICAL for zero-log chain
- **WHEN** 调用链上所有步骤均无应用级日志语句（0% 覆盖率）
- **THEN** 系统 SHALL 标记严重度为 🔴 P0 严重，附带说明"整条调用链完全无应用级日志，故障无法追踪"

#### Scenario: Severity CRITICAL for missing entry log
- **WHEN** 调用链入口方法无日志语句
- **THEN** 系统 SHALL 标记严重度为 🔴 P0 严重，附带说明"入口方法无日志，不可追踪触发点"

#### Scenario: Severity CRITICAL for missing exception log
- **WHEN** 调用链中存在 catch 块或 throws 声明但无常量ERROR/WARN日志
- **THEN** 系统 SHALL 标记严重度为 🔴 P0 严重，附带说明"关键异常处理无日志，异常时无诊断信息"

#### Scenario: Severity WARNING for missing exit log
- **WHEN** 调用链出口方法无日志语句
- **THEN** 系统 SHALL 标记严重度为 🟡 P1 警告，附带说明"出口无日志，不可追踪完成状态"

#### Scenario: Severity WARNING for coverage below 50%
- **WHEN** 调用链覆盖率 < 50% 且缺失步骤 ≥ 3
- **THEN** 系统 SHALL 标记严重度为 🟡 P1 警告，附带说明"关键路径日志覆盖不足"

#### Scenario: Severity OK for coverage above 50%
- **WHEN** 调用链覆盖率 ≥ 50% 但有 1-2 步缺失
- **THEN** 系统 SHALL 标记严重度为 🟢 P2 合格，附带说明"日志覆盖基本完整，可考虑补充"

#### Scenario: Severity takes highest match
- **WHEN** 同一调用链满足多个严重度条件
- **THEN** 系统 SHALL 取最高严重度（P0 > P1 > P2）

### Requirement: Output severity assessment in log trigger report
系统 SHALL 在 CP2 日志触发报告和 CP4 日志文档中包含严重度评估结果。

#### Scenario: Severity appears in CP2 log trigger report
- **WHEN** CP2 输出日志触发标记清单
- **THEN** 系统 SHALL 对每个触发项标注严重度等级（🔴P0 / 🟡P1 / 🟢P2）和判定原因

#### Scenario: Severity appears at top of log doc §5
- **WHEN** CP4 生成模块级日志 §5
- **THEN** 系统 SHALL 在 §5 顶部输出告警框，包含严重度图标、调用链名称、覆盖率、关键问题描述、受影响步骤数
