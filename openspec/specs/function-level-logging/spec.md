## Requirements

### Requirement: Generate function-level log document for high-complexity functions
系统 SHALL 为 codegraph 检测出的高复杂度函数和核心方法生成独立的函数级日志文档（`{函数名}_关键函数日志.md`），置于模块日志目录下。

#### Scenario: Generate function log for cognitive complexity > 10
- **WHEN** codegraph 可用且某函数的认知复杂度 > 10
- **THEN** 系统 SHALL 在 `docs/logs/{所属模块}/{函数名}_关键函数日志.md` 生成包含以下章节的函数级日志文档：§1 函数概述（用途、签名、复杂度、调用者列表、代码位置）、§2 执行路径日志追踪（调用链步骤 | 日志语句 | 级别 | 文件:行号 | 内容摘要）、§3 异常传播路径、§4 日志覆盖评估、§5 故障排查指南（Mermaid 决策树）

#### Scenario: Generate function log for invocation count > 3
- **WHEN** codegraph 可用且某函数的被调用次数 > 3
- **THEN** 系统 SHALL 为该函数生成函数级日志文档，与认知复杂度触发相同的章节结构

#### Scenario: Generate function log for role=core nodes
- **WHEN** codegraph 可用且某节点 role=core
- **THEN** 系统 SHALL 为该 core 节点生成函数级日志文档

#### Scenario: Skip function log when codegraph unavailable
- **WHEN** codegraph 不可用（手动模式）
- **THEN** 系统 SHALL NOT 生成函数级日志文档，仅在模块级日志中标注"[手动模式] 复杂函数日志未生成，建议安装 @optave/codegraph"

#### Scenario: Multiple triggers for same function deduplicate
- **WHEN** 同一函数同时满足多个触发条件（例如既是 core 节点且认知复杂度 > 10）
- **THEN** 系统 SHALL 仅生成一份函数级日志文档，在文档头部标注所有匹配的触发原因

### Requirement: Generate scenario-level log document for cross-module call chains
系统 SHALL 为 codegraph 检测到的跨模块高复杂度调用链生成端到端场景日志文档（`{场景名}_场景日志.md`）。

#### Scenario: Generate scenario log for cross-module chain with >= 6 steps
- **WHEN** codegraph 检测到跨模块调用链步数 ≥ 6 且涉及参与者 ≥ 4
- **THEN** 系统 SHALL 在 `docs/logs/{主要模块}/{场景名}_场景日志.md` 生成包含以下内容：§1 场景概述（触发条件、涉及模块列表）、§2 端到端日志追踪（Mermaid 时序图 + 每步骤日志语句表）、§3 各模块日志连接点、§4 异常传播全景、§5 故障定位指南

#### Scenario: Scenario log references function logs
- **WHEN** 场景调用链上的某步骤已有对应的函数级日志文档
- **THEN** 系统 SHALL 在场景日志中引用该函数日志文档（链接或引用标注），避免内容重复

#### Scenario: Skip scenario log when codegraph unavailable
- **WHEN** codegraph 不可用
- **THEN** 系统 SHALL NOT 生成场景日志文档，在模块级日志中标注"[手动模式] 场景日志未生成"

### Requirement: Function-level log document standard structure
函数级日志文档 SHALL 遵循规范结构。

#### Scenario: Required chapters in function log
- **WHEN** 生成函数级日志文档
- **THEN** 系统 SHALL 包含以下章节：§1 函数概述（触发原因标注、用途、签名、复杂度：认知 X | 圈 Y、主要调用者、代码位置）、§2 执行路径日志追踪（表格：步骤 | 方法 | 日志存在 | 级别 | 内容 | 文件:行号）、§3 异常传播路径（表格：步骤 | 方法 | 可能异常 | 是否捕获 | 日志表现 | 传播方式）、§4 日志覆盖评估（有日志步骤数/总步骤数 × 100% + 缺失标注）、§5 故障排查指南（Mermaid 决策树：日志关键词 → 可能原因 → 验证步骤 → 修复建议 + 日志过滤命令）

#### Scenario: Function log header includes trigger reason
- **WHEN** 生成函数级日志文档
- **THEN** 系统 SHALL 在文档头部标注触发原因，格式为：`> 触发原因: [认知复杂度: 42] [被调用次数: 15] [codegraph role=core] [跨模块调用链]`

### Requirement: Scenario-level log document standard structure
场景日志文档 SHALL 遵循规范结构。

#### Scenario: Required chapters in scenario log
- **WHEN** 生成场景日志文档
- **THEN** 系统 SHALL 包含以下章节：§1 场景概述（业务场景名、触发条件、涉及模块列表含代码位置）、§2 端到端日志追踪（Mermaid 时序图标注每步日志存在性 + 步骤日志明细表）、§3 各模块日志连接点（模块间日志关联：A 模块的 INFO 对应 B 模块的 DEBUG 等）、§4 异常传播全景（跨模块异常传播 Mermaid + 每个传播节点的日志表现）、§5 故障定位指南（按症状 → 日志关键词 → 定位模块 → 排查步骤）
