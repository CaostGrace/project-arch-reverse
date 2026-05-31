## MODIFIED Requirements

### Requirement: Function-level log document with severity and recommendations
系统 SHALL 在函数级日志文档中包含严重度评估和可执行的改进建议。

#### Scenario: §4 severity alert for function log
- **WHEN** 生成函数级日志 §4 日志覆盖评估
- **THEN** 系统 SHALL 在覆盖率统计上方输出严重度告警框（与模块级日志同格式），下方附加该函数的建议埋点表

#### Scenario: §5 troubleshooting with expected output
- **WHEN** 生成函数级日志 §5 故障排查指南
- **THEN** 系统 SHALL 每个排查步骤附带平台对应的 grep 命令、预期输出示例和无输出诊断方向
