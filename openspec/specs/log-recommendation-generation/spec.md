## Requirements

### Requirement: Generate actionable logging recommendations
系统 SHALL 基于 codegraph 函数元数据自动生成具体的日志埋点建议。

#### Scenario: Infer recommendation for entry method
- **WHEN** 调用链入口方法无日志且函数名含 start/begin/init/sync/trigger 等动作词
- **THEN** 系统 SHALL 生成建议：级别 INFO，内容 "{动作名} started, {关键参数}"，优先级 P0，原因标注"入口无日志"

#### Scenario: Infer recommendation for network call
- **WHEN** 函数名含 request/fetch/call/send 等网络动作词且无日志
- **THEN** 系统 SHALL 生成建议：请求端 INFO "Request {url/endpoint}"，返回端 INFO "Response {status}"，优先级 P1

#### Scenario: Infer recommendation for data write
- **WHEN** 函数名含 insert/save/write/create 等写入动作词且无日志
- **THEN** 系统 SHALL 生成建议：级别 INFO，内容 "Inserted/Saved {n} rows, {key}"，优先级 P1

#### Scenario: Infer recommendation for exception handler
- **WHEN** catch 块中无日志语句
- **THEN** 系统 SHALL 生成建议：级别 ERROR，内容 "{ExceptionType}: {message}", exception，优先级 P0

#### Scenario: Infer recommendation with parameter names
- **WHEN** 函数有明确参数名
- **THEN** 系统 SHALL 将关键参数名填入建议日志内容占位符，如 "userId={userId}, count={items.size()}"

#### Scenario: Generate recommendation table format
- **WHEN** 输出埋点建议
- **THEN** 系统 SHALL 生成 5 列表格：优先级 | 代码位置（文件:行号）| 建议日志内容 | 建议级别 | 触发原因

### Requirement: Cross-module recommendation for scenario logs
系统 SHALL 为场景日志生成跨模块的埋点建议。

#### Scenario: Scenario log recommendations grouped by module
- **WHEN** 场景涉及多个模块的调用链且存在日志缺失
- **THEN** 系统 SHALL 在场景日志 §6 中按模块分组输出埋点建议，标注所属模块名

#### Scenario: Prioritize cross-module recommendations
- **WHEN** 场景日志有多条建议
- **THEN** 系统 SHALL 按优先级排序：P0 跨模块入口/出口 > P0 单模块 > P1 跨模块 > P1 单模块
