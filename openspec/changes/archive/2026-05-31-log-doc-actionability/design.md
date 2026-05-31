## Context

上一轮 change 已建立两层日志生成体系，但日志文档停留在"代码扫描报告"层面——展示日志语句存在/缺失的数据表，缺少三个关键能力：(1) 严重度判定——0% 覆盖率和 50% 覆盖率被同等对待，(2) 改进建议——只标注"缺失"不告诉用户该加什么，(3) 输出示例——文档描述日志但不展示实际 grep 能看到的内容。本设计将日志文档从"扫描清单"升级为"可执行的排查手册"。

## Goals / Non-Goals

**Goals:**
- 严重度分级：🔴严重（整链零日志/入口缺失/异常缺失）→ 🟡警告（出口缺失/覆盖<50%）→ 🟢合格
- 自动埋点建议：基于函数名语义 + 参数 + 调用链位置推断日志内容、级别、优先级
- 实际输出示例：各平台真实日志格式（时间戳、TAG、PID/TID、消息），开发者可直接参照 grep
- Grep 即用命令：每条日志附带平台对应 grep/filter 命令，预期输出，无输出时的诊断方向

**Non-Goals:**
- 不自动修改源代码添加日志（这只是文档建议）
- 不改变日志提取的核心逻辑（grep 日志语句的方式不变）
- 不改变 CP0-CP7 的流程结构

## Decisions

### Decision 1: 严重度判定在执行路径分析时完成

**选择**：在 CP2 codegraph 分析管道中，Step A.9（日志追踪）完成后立即对每条调用链执行严重度判定，结果写入日志触发标记清单。

**理由**：
- 数据已在 CP2 收集完毕（每一步的日志存在/缺失），无需重新扫描
- CP4 只需读取判定结果写入文档，无需二次分析
- 与已有的"日志触发标记清单"输出结构一致

**判定规则**：

| 条件 | 严重度 | 说明 |
|------|:---:|------|
| 整条调用链 0 条应用级日志 | 🔴 P0 | 故障完全不可追踪 |
| 入口方法无日志 | 🔴 P0 | 不可追踪触发点 |
| 关键异常处理（catch 块）无日志 | 🔴 P0 | 异常时无诊断信息 |
| 出口无日志 | 🟡 P1 | 不可追踪完成状态 |
| 覆盖率 < 50% 且缺失 ≥ 3 步 | 🟡 P1 | 关键路径日志不足 |
| 覆盖率 ≥ 50% 但有 1-2 步缺失 | 🟢 P2 | 可追踪，可优化 |

> 满足任一升级条件即标记对应严重度（取最高）。

### Decision 2: 埋点建议用函数语义推断

**选择**：基于 codegraph 提供的函数名、参数名、调用链角色（入口/中间/出口）推断建议。不依赖 AI 推测具体业务语义。

**推断规则**：

| 函数名特征 | 推断内容模式 | 级别 |
|-----------|------------|------|
| 含 start/begin/init/trigger + 入口角色 | "{action} started, {key params}" | INFO |
| 含 request/fetch/call/send | "Request {url/endpoint}" + 返回"Response {status}" | INFO |
| 含 insert/save/write/create | "Inserted/Saved {n} rows, {key}" | INFO |
| 含 query/load/find/get + 中间角色 | "Loading {entity}, {query params}" | DEBUG |
| 含 send/notify/push + 出口角色 | "Notification sent, {recipient}" | INFO |
| catch 块 | "{exception type}: {message}", e | ERROR |
| 通用入口 | "{ClassName}.{methodName}() called, {params}" | INFO |

**优先级推断**：
- P0：入口/异常处理 → 故障定位必须
- P1：出口/数据写入 → 关键业务节点
- P2：中间步骤/debug信息 → 可选增强

### Decision 3: 各平台输出示例用通用模板生成

**选择**：为每种项目类型预设日志输出格式模板，填入从代码提取的 TAG 和消息内容生成示例值。

**模板速查**：

| 平台 | 模板 |
|------|------|
| Android | `MM-DD HH:MM:SS.mmm  PID  TID  I  TAG: message` |
| iOS | `YYYY-MM-DD HH:MM:SS.mmm+TZ  App[PID:TID] [TAG] message` |
| Java后端 | `YYYY-MM-DD HH:MM:SS.mmm [thread] LEVEL  logger - message` |
| Node.js | `YYYY-MM-DDTHH:MM:SS.mmmZ [level]: message {json}` |
| Flutter | `[TAG] message {param: value}` |
| HarmonyOS | `MM-DD HH:MM:SS.mmm  PID  TID  I  domain/TAG: message` |

**参数值填充**：使用占位符形式 `{参数名}`，保留语义可读性。

### Decision 4: 文档章节改动嵌入现有章节，不新增章节号

**选择**：模块日志 §5 顶部增加告警框，§6 表格增加列，§8 增强命令格式。仅在场景日志新增 §6，根文档在现有结构后新增日志汇总。

**理由**：
- 最小化章节号变动，不影响已在引用的交叉引用
- 模块日志 §5 和函数日志 §4 本身是"覆盖评估"，自然承载告警和建议
- 场景日志目前 5 章，§6 独立增加改进建议符合其"端到端分析"定位

## Risks / Trade-offs

- [风险] 严重度误判——简单工具函数零日志被标严重
  → 缓解：严重仅对"日志触发标记"的调用链判定，工具函数不会被标记为触发对象
- [风险] 输出示例过于模板化，缺乏真实感
  → 缓解：用代码实际 TAG 和消息内容填充，参数用语义占位符，开发者可以直观匹配
- [权衡] 文档长度增加约 30% vs 排查效率大幅提升
  → 选择增加：日志文档的核心价值就是排查，值得投入篇幅
