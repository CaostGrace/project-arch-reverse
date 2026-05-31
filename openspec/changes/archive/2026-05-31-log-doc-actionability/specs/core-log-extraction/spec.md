## MODIFIED Requirements

### Requirement: Generate log documents for all log-trigger modules
系统 SHALL 为日志触发模块列表中的每个模块生成日志文档，文档包含严重度评估、埋点建议和实际输出示例。

#### Scenario: Generate log doc for any log-trigger module
- **WHEN** 模块在日志触发模块列表中
- **THEN** 系统 SHALL 创建 `docs/logs/{模块名}/{模块名}_核心日志.md`，codegraph 模式下包含全部 8 章。§5 包含严重度告警框 + 建议埋点表。§6 每条日志包含"输出示例"和"grep命令"列。§7 包含本平台日志格式速查。§8 排查步骤附带预期输出示例和 fallback 诊断。

#### Scenario: §5 severity alert box format
- **WHEN** 生成 §5 日志覆盖评估
- **THEN** 系统 SHALL 在评估表上方输出告警框：
```
> ⚠️ 严重度: 🔴 P0 严重 — 同步链路完全无应用级日志，故障无法追踪
> 受影响步骤: 6/6 (覆盖率 0%)
```
格式随严重度变化：🔴P0 使用 `🚨` 前缀，🟡P1 使用 `⚠️`，🟢P2 使用 `💡`

#### Scenario: §5 recommendation table below alert
- **WHEN** §5 生成告警框后
- **THEN** 系统 SHALL 附加 5 列建议埋点表：优先级 | 代码位置（文件:行号）| 建议日志内容 | 建议级别 | 触发原因

#### Scenario: Log output example in log texts
- **WHEN** 系统 SHALL TO BE 生成 §6 日志清单表格
- **THEN** 增加"实际输出示例"列和"grep命令"列，根据项目类型生成对应平台的输出示例文本和过滤命令

#### Scenario: §7 platform log format reference
- **WHEN** 生成 §7 日志级别说明与排查过滤
- **THEN** 系统 SHALL 追加"本平台日志格式"小节：日志行格式模板、各字段含义（每列一个字段说明）、默认过滤命令示例、平台特有注意事项

#### Scenario: §8 troubleshooting step with expected output
- **WHEN** 生成 §8 故障排查决策树
- **THEN** 系统 SHALL 每个排查步骤包含 4 要素：① 排查命令（可直接复制执行）② 预期正常输出示例 ③ 预期异常输出示例 ④ 无输出时的诊断方向和下一步

### Requirement: Generate scenario log with improvement recommendations
系统 SHALL 为场景日志生成跨模块改进建议章节。

#### Scenario: Scenario log §6 improvement recommendations
- **WHEN** 生成场景日志文档
- **THEN** 系统 SHALL 包含 §6 改进建议：按模块分组列出建议埋点（优先级 | 代码位置 | 建议内容 | 级别 | 所属模块），顶部展示跨模块严重度总结

## ADDED Requirements

### Requirement: Generate log deficiency summary in root document
系统 SHALL 在根文档中生成日志缺陷汇总章节。

#### Scenario: Root doc log deficiency summary
- **WHEN** 生成或聚合根文档
- **THEN** 系统 SHALL 在 §22/§23 之后追加日志缺陷汇总章：① 全局统计（🔴严重 X / 🟡警告 Y / 🟢合格 Z）② P0 严重缺陷列表（含调用链描述、涉及模块、覆盖率、详见链接）③ P1 警告列表
