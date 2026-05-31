## Why

当前日志生成以"核心模块"为唯一门槛：只有通过 CP0 四维度（用户可达/后台运行/复杂度>20文件或>5000行/用户指定）判定为核心模块的才会生成日志文档。非核心模块即便包含 codegraph 检测出的认知复杂度>15、被调用次数>10 的高复杂度函数，也完全跳过日志文档生成。这导致系统性遗漏：跨模块核心流程中落在非核心模块的关键步骤日志不可见，高复杂度函数的错误传播路径无文档可查。必须将日志触发条件从"模块级"下沉到"函数/流程级"，让 codegraph 的数据驱动日志覆盖而非仅依赖粗糙的模块分类。

## What Changes

- **BREAKING**: CP0 核心模块判定阈值下调：文件数 20→10，代码行数 5000→2000，扩大模块级日志覆盖
- **BREAKING**: 解耦日志生成触发与核心模块判定。新增独立日志触发条件：codegraph 认知复杂度>10、被调用次数>3、处于跨模块关键调用链上，满足任一即触发日志生成（无论所在模块是否为核心）
- **新增** 两层日志体系：第一层模块级核心日志(8章) + 第二层函数级/场景级精细日志
- **新增** `{函数名}_关键函数日志.md`：为 codegraph 检测出的高复杂度函数、核心方法生成独立日志文档
- **新增** `{场景名}_场景日志.md`：为跨模块高复杂度调用链生成端到端场景日志
- CP2 codegraph 分析结果扩展输出"日志触发标记列表"，输入 CP4 日志生成
- CP4 生成流程扩展为两层：先模块级日志 → 再函数/场景级日志
- 手动模式降级：codegraph 不可用时用 grep 近似估算（圈复杂度近似/函数引用计数），仅生成模块级日志，文档头部标注"[手动模式] 复杂度为近似值"
- CP6 自检 Step 5 扩展为两层日志完整性检查

## Capabilities

### New Capabilities

- `function-level-logging`: 为 codegraph 检测出的高复杂度函数和核心方法生成独立的函数级日志文档（{函数名}_关键函数日志.md），包含执行路径日志追踪、异常传播、故障排查指南。为跨模块核心场景生成端到端场景日志文档（{场景名}_场景日志.md）。
- `complexity-driven-log-trigger`: 将日志生成触发条件从模块维度扩展到函数维度，使用 codegraph 精确的认知复杂度、被调用次数、调用链数据独立判定日志生成需求，与核心模块判定解耦。

### Modified Capabilities

- `incremental-detection`: 核心模块复杂度阈值从>20文件/5000行下调至>10文件/2000行；新增独立的"日志触发模块"判定逻辑（核心模块 ∪ codegraph高复杂度函数所在模块 ∪ codegraph关键调用链模块），在 CP0 检测报告中输出并等待用户确认
- `core-log-extraction`: 日志提取范围从"仅核心模块"扩展为"日志触发模块列表"；新增第二层函数级/场景级日志文档的提取规则和输出格式
- `execution-path-logging`: 执行路径日志追踪扩展到非核心模块中 codegraph 检测出的高复杂度函数；场景日志文档中的跨模块端到端日志追踪表
- `self-check-verification`: CP6 Step 5 扩展为检测两层日志文档完整性（模块级 + 函数/场景级）；手动模式时检查标注是否存在

## Impact

- SKILL.md: CP0、CP2、CP4、CP6 流程修订，文档输出结构更新
- references/chapters-ref.md: 新增第二层日志章节定义
- references/doc-format.md: 新增关键函数日志和场景日志格式
- references/log-extraction-guide.md: 提取范围扩展，手动模式近似方法
- references/quick-ref.md: 策略表更新
- references/self-check-spec.md: CP6 自检两层日志检查
- openspec/specs/incremental-detection/: 阈值调整 + 日志触发判定
- openspec/specs/core-log-extraction/: 范围扩展 + 两层日志
- openspec/specs/execution-path-logging/: 扩展至非核心模块
- openspec/specs/self-check-verification/: 两层日志检查
