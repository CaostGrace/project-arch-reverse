## Why

当前日志文档只是代码扫描报告：列出日志语句的位置和内容摘要，但没有严重度判定、没有可执行的改进建议、没有各平台实际的日志输出示例。用户拿到文档后仍需回到源码逐行阅读 `Log.i(TAG, "xxx")` 才能知道真实日志长什么样、怎么 grep 排查。当发现"整个同步链路零日志"这样的关键缺陷时，文档只显示"覆盖率 0%"，不会主动建议"补充 6 个关键埋点"。日志文档从"排查手册"降级为"扫描清单"，失去实用价值。

## What Changes

- **新增** 日志缺陷严重度分级模型：🔴严重（整链零日志/入口缺失）→ 🟡警告（出口缺失/覆盖<50%）→ 🟢合格
- **新增** 自动埋点建议生成：基于 codegraph 函数名、参数、调用链位置推断建议的日志内容、级别和优先级
- **新增** 各平台日志实际输出示例：Android / iOS / Java后端 / Node.js / Flutter / HarmonyOS 的真实日志格式展示
- **新增** 日志文档 §7 "各平台日志格式速查"：时间戳格式、TAG位置、PID/TID、默认过滤命令
- **修改** 模块级日志 §5：顶部增加严重度告警框 + 建议埋点表（优先级 | 位置 | 建议内容 | 级别）
- **修改** 模块级日志 §6：每行增加"实际输出示例"列 + "grep命令"列
- **修改** 模块级日志 §8：故障排查步骤拆解为逐步命令 + 预期输出 + 无输出时的诊断方向
- **修改** 函数级日志 §4：顶部增加严重度告警 + 建议埋点
- **修改** 函数级日志 §5：逐步排查命令 + 预期输出示例
- **新增** 场景日志 §6 "改进建议"：跨模块埋点建议，按模块分组
- **新增** 根文档 "日志缺陷汇总"（在 §22/§23 之后）：全项目日志覆盖统计 + P0 严重缺陷列表

## Capabilities

### New Capabilities

- `log-severity-assessment`: 日志缺陷严重度分级（🔴严重/🟡警告/🟢合格），基于覆盖率、入口/出口/异常处理的日志存在性综合判定
- `log-recommendation-generation`: 基于 codegraph 函数元数据（名称、参数、调用链位置）自动推断建议日志内容、级别和埋点优先级
- `log-output-examples`: 为每个平台的每条日志生成实际输出示例和直接可用的 grep 排查命令

### Modified Capabilities

- `core-log-extraction`: §5 日志覆盖评估增强为"严重度告警 + 建议埋点表"；§6 日志清单增加输出示例列和 grep 命令列；§7 增加各平台日志格式速查
- `function-level-logging`: §4 日志覆盖评估增强为告警+建议；§5 故障排查指南增加逐步命令和预期输出
- `architecture-doc-generation`: 根文档新增"日志缺陷汇总"章节
- `doc-format`: 新增严重度告警框格式、建议埋点表格式、日志输出示例列格式

## Impact

- SKILL.md: CP2 新增严重度判定 + 埋点建议生成步骤；CP4 日志生成增强
- references/chapters-ref.md: 模块日志、函数日志、场景日志章节说明更新
- references/doc-format.md: 新增告警框/建议表/输出示例/各平台格式速查的模板
- references/log-extraction-guide.md: 新增各平台日志输出格式参考
- openspec/specs/core-log-extraction/: §5/§6/§7/§8 增强
- openspec/specs/function-level-logging/: §4/§5 增强
- openspec/specs/architecture-doc-generation/: 根文档日志汇总章节
