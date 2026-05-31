## ADDED Requirements

### Requirement: Execute CP2 result confirmation
系统 SHALL 在文档生成完成后展示结果并等待用户反馈（CP2）。

#### Scenario: User confirms satisfaction
- **WHEN** 用户回复"满足需求"
- **THEN** 系统 SHALL 直接进入 CP2.5（自检验证）

#### Scenario: User requests modifications
- **WHEN** 用户提出具体修改要求（如"第7章依赖关系有误"）
- **THEN** 系统 SHALL 执行修改 → 更新文档 → 返回 CP2 重新确认

#### Scenario: User requests manual review
- **WHEN** 用户回复"需要人工审核"
- **THEN** 系统 SHALL 跳过 CP2.5 → 直接进入 CP3（人工更正）

### Requirement: Execute comprehensive self-check with no iteration limit
系统 SHALL 执行自检循环直到全部检查项通过。文档缺失类问题不可通过"用户接受"退出。

#### Scenario: Self-check passes on first attempt
- **WHEN** 第 1 次自检全部 8 项通过
- **THEN** 系统 SHALL 报告"自检全部通过"，流程完成

#### Scenario: Self-check finds document-missing auto-fixable issues
- **WHEN** 自检发现模块缺失、日志文档缺失、章节缺失
- **THEN** 系统 SHALL 🔴 必须立即修复，不可询问用户，不可标记"用户接受"。修复后重新全项自检

#### Scenario: Self-check finds other auto-fixable issues
- **WHEN** 自检发现依赖不一致、聚合错误等其他可自动修复问题
- **THEN** 系统 SHALL 立即修复 → 重新执行全项自检

#### Scenario: Same issue fails auto-fix 3 times
- **WHEN** 同一检查项的同一问题经 3 次自动修复仍无法通过
- **THEN** 系统 SHALL 标记该问题为阻塞项 → 委托 CP7 处理 → CP7 完成后回归 CP6 重新自检

#### Scenario: Five full rounds reached with remaining blockers
- **WHEN** 全轮自检累计 5 轮后仍有阻塞项（非文档缺失类）
- **THEN** 系统 SHALL 暂停并请求用户决策：继续自检、降级接受残留、或永久跳过该检查项

#### Scenario: User accepts residual only for CP7 delegated items
- **WHEN** CP7 处理完成后仍存在无法修复的阻塞项（架构判断/非标准结构）
- **THEN** 系统 SHALL 允许用户将该阻塞项标记为"用户接受"，后续自检跳过
- **AND** 系统 SHALL NOT 允许将文档缺失类问题标记为"用户接受"

### Requirement: Check module coverage and completeness (Step 1)
系统 SHALL 验证所有模块文档的覆盖率和每文档 12 章完整性。

#### Scenario: All modules have complete docs
- **WHEN** 构建文件识别 N 个模块，已生成 M 个模块文档，且每文档 12 章完整
- **THEN** 系统 SHALL 标记 Step 1 通过（M ≥ N 且每文档 12 章不缺）

#### Scenario: Module document missing
- **WHEN** 某构建文件识别的模块缺少模块文档
- **THEN** 系统 SHALL 立即生成缺失的模块文档 → 重新自检

#### Scenario: Module document has missing chapters
- **WHEN** 某模块文档章节数不足 12 或存在占位符
- **THEN** 系统 SHALL 补充缺失章节 → 重新自检

#### Scenario: Task checklist comparison
- **WHEN** `.task-checklist.md` 存在
- **THEN** 系统 SHALL 对比任务清单预期任务数 vs `[x]` 完成数 → `[!]` 跳过项列为"已知不完整" → 未执行的 `[ ]` 项列为严重缺失并立即生成

### Requirement: Verify inter-module dependency consistency (Step 2)
系统 SHALL 交叉验证模块间依赖声明的一致性。

#### Scenario: Dependency declaration cross-check
- **WHEN** 模块 A 的第3章声明依赖模块 B
- **THEN** 系统 SHALL 确认模块 B 的模块文档存在，且依赖方向与构建文件解析一致

#### Scenario: Dependency direction mismatch
- **WHEN** 模块 A 文档声明依赖 B，但构建文件解析显示 A 依赖 C 而非 B
- **THEN** 系统 SHALL 以构建文件解析为准修正 A 模块第3章

### Requirement: Check root document completeness (Step 3)
系统 SHALL 逐章检查根文档 21 章的完整性和非空性。

#### Scenario: Chapter is missing
- **WHEN** 根文档某章节不存在
- **THEN** 系统 SHALL 标记缺失章节并立即生成

#### Scenario: Chapter has placeholder content
- **WHEN** 根文档某章节内容为空或仅含占位符（如"待补充"、"TODO"）
- **THEN** 系统 SHALL 标记为不完整并重新聚合填充

### Requirement: Verify root document aggregation correctness (Step 4)
系统 SHALL 验证根文档聚合内容与模块文档的一致性。

#### Scenario: Dependency graph vs module declarations
- **WHEN** 根文档第6章依赖图中存在 A→B 的边
- **THEN** 系统 SHALL 确认模块 A 的第3章声明了对 B 的依赖

#### Scenario: Business flow vs module flow
- **WHEN** 根文档 §12.3 展示的核心功能
- **THEN** 系统 SHALL 确认该功能在对应模块文档第6章中存在且步骤一致

#### Scenario: ER diagram vs module data dependencies
- **WHEN** 根文档第13章 ER 图包含某实体
- **THEN** 系统 SHALL 确认至少一个模块文档第7章引用了该实体

### Requirement: Check core log document completeness (Step 5)
系统 SHALL 验证两层日志文档的完整性和日志覆盖范围。

#### Scenario: All log-trigger modules have first-layer log docs
- **WHEN** 日志触发模块列表中的模块有完整的模块级日志文档（`{模块}_核心日志.md`，8章/codegraph 或 5章/手动）
- **THEN** 系统 SHALL 标记第一层日志文档完整

#### Scenario: All triggered functions have second-layer log docs
- **WHEN** codegraph 可用且所有标记为"需生成函数级日志"的函数都有 `{函数名}_关键函数日志.md`
- **THEN** 系统 SHALL 标记第二层函数日志文档完整

#### Scenario: All triggered scenarios have second-layer log docs
- **WHEN** codegraph 可用且所有标记为"需生成场景日志"的调用链都有 `{场景名}_场景日志.md`
- **THEN** 系统 SHALL 标记第二层场景日志文档完整

#### Scenario: Log-trigger module missing first-layer log doc
- **WHEN** 日志触发模块列表中的模块缺少 `docs/logs/{模块名}/` 目录
- **THEN** 系统 SHALL 立即调用日志提取流程生成第一层模块级日志文档

#### Scenario: Auto-check log coverage
- **WHEN** 日志触发模块的日志文档第3章覆盖了所有核心功能，每个功能 ≥3 条关键日志
- **THEN** 系统 SHALL 标记日志覆盖合格

#### Scenario: Manual mode annotation check
- **WHEN** codegraph 不可用
- **THEN** 系统 SHALL 检查所有日志文档头部是否包含 `[手动模式]` 标注

#### Scenario: Architecture change without log sync
- **WHEN** 架构文档有更新但对应模块的日志文档（第一层 + 第二层）未同步
- **THEN** 系统 SHALL 立即同步更新所有层次的日志文档

### Requirement: Verify aggregated chart cross-reference consistency (Step 6)
系统 SHALL 验证根文档第12章聚合图表与模块文档 §6 的一致性。

#### Scenario: Sequence diagram step count consistency
- **WHEN** 根文档 §12.3 中某核心功能时序图的调用步骤数
- **THEN** 系统 SHALL 确认与对应模块文档第6章中同一功能的时序图步骤数一致

#### Scenario: Participant list consistency
- **WHEN** 根文档中时序图列出了参与者列表 [A, B, C, D]
- **THEN** 系统 SHALL 确认模块文档中对应时序图的参与者列表相同

#### Scenario: Call order consistency in cross-module diagram
- **WHEN** 根文档 §12.4 中跨模块时序图描述 A 调用 B 再调用 C
- **THEN** 系统 SHALL 确认 A 模块第6章和 B 模块第6章中对应的调用链与此一致

### Requirement: Delegate blocked items to CP3 and resume self-check
系统 SHALL 支持不可自动修复的阻塞项委托 CP3 处理，处理完成后自动回归 CP2.5。

#### Scenario: Auto-fix exhausted, delegate to CP3
- **WHEN** 某检查项的同一问题经 3 次自动修复仍失败
- **THEN** 系统 SHALL 标记该问题为阻塞项 → 列出阻塞清单 → 进入 CP3 委托处理

#### Scenario: CP3 resolves blocked item, return to self-check
- **WHEN** CP3 完成阻塞项的人工处理
- **THEN** 系统 SHALL 自动回归 CP2.5，从 Step 1 开始全项重新自检

#### Scenario: Non-standard structure cannot be auto-resolved
- **WHEN** 项目结构非标准无法自动解析（如自定义构建系统）
- **THEN** 系统 SHALL 立即标记为阻塞 → 委托 CP3（不做 3 次自动修复尝试）

### Requirement: Generate formatted self-check report
系统 SHALL 按照标准格式输出自检报告。

#### Scenario: Output self-check report
- **WHEN** 每次自检完成
- **THEN** 系统 SHALL 输出包含以下部分的报告：项目信息、模块覆盖与完整性、模块间依赖一致性、根文档完整性、根文档聚合正确性、核心日志文档状态、聚合图表交叉验证、本次发现的问题、阻塞项清单

#### Scenario: Output final report when self-check completes
- **WHEN** 自检全部通过
- **THEN** 系统 SHALL 输出最终报告：全部通过的检查项、用户接受的残留项（如有）、文档生成状态汇总

### Requirement: Handle problem types during self-check
系统 SHALL 按问题类型执行对应的修复操作。

#### Scenario: Root doc chapter missing
- **WHEN** 根文档章节缺失
- **THEN** 系统 SHALL 立即生成缺失章节，不询问用户

#### Scenario: Mermaid chart syntax error
- **WHEN** 发现 Mermaid 图表语法错误（如节点未定义、括号冲突）
- **THEN** 系统 SHALL 重新生成正确的图表

#### Scenario: Broken link
- **WHEN** 发现文档间的链接失效
- **THEN** 系统 SHALL 修复链接

#### Scenario: Dependency relation error
- **WHEN** 发现模块依赖关系错误
- **THEN** 系统 SHALL 修正依赖关系描述，重新生成相关图表

#### Scenario: Document missing is never accepted as residual
- **WHEN** 自检发现模块文档/日志文档缺失
- **THEN** 系统 SHALL 🔴 必须生成，不可标记为"用户接受残留"或"基础完成"

### Requirement: Mermaid syntax secondary validation (Step 7)
系统 SHALL 在 CP6 自检中对所有文档的 Mermaid 代码块执行二次语法校验。

#### Scenario: Rescan all mermaid blocks
- **WHEN** CP6 Step 7 执行
- **THEN** 系统 SHALL 提取所有 ` ```mermaid ` 代码块，逐块执行 10 条正则规则扫描

#### Scenario: Fix newly found mermaid issues
- **WHEN** Step 7 发现门禁1未覆盖的新 Mermaid 语法问题
- **THEN** 系统 SHALL 自动修复 → 重新自检；同问题 3 次失败 → 标记阻塞 → 委托 CP7

### Requirement: Document structure integrity check (Step 8)
系统 SHALL 在 CP6 自检中对文档结构、表格、链接、代码块执行全局校验。

#### Scenario: Global table column validation
- **WHEN** CP6 Step 8 执行
- **THEN** 系统 SHALL 对所有文档的表格逐表校验表头/分隔行/数据行的列数一致性

#### Scenario: Internal link validity check
- **WHEN** CP6 Step 8 执行
- **THEN** 系统 SHALL 提取所有 Markdown 相对链接，验证目标文件存在

#### Scenario: Code block validation
- **WHEN** CP6 Step 8 执行
- **THEN** 系统 SHALL 检查代码块语言标识合法性和闭合配对

#### Scenario: Empty chapter and heading level check
- **WHEN** CP6 Step 8 执行
- **THEN** 系统 SHALL 检测空章节、标题层级跳跃、引用块闭合

#### Scenario: Aggregate skip items from both gates
- **WHEN** CP6 最终报告生成
- **THEN** 系统 SHALL 汇总门禁1和门禁2的全部跳过项，含文档位置、问题类型、跳过原因
