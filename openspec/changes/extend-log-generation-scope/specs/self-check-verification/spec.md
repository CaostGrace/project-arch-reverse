## MODIFIED Requirements

### Requirement: Verify core log document completeness and architecture change synchronization
系统 SHALL 在 Step 5 中检查两层日志文档的完整性。

#### Scenario: Module-level log docs exist for all log-trigger modules
- **WHEN** 自检 Step 5 执行
- **THEN** 系统 SHALL 检查日志触发模块列表中的所有模块是否都有对应的模块级核心日志文档（`docs/logs/{模块}/{模块}_核心日志.md`）

#### Scenario: Function-level log docs exist for all triggered functions
- **WHEN** 自检 Step 5 执行且 codegraph 可用
- **THEN** 系统 SHALL 检查日志触发标记清单中所有标记为"需生成函数级日志"的函数是否都有对应的 `{函数名}_关键函数日志.md` 文档

#### Scenario: Scenario-level log docs exist for all triggered scenarios
- **WHEN** 自检 Step 5 执行且 codegraph 可用
- **THEN** 系统 SHALL 检查日志触发标记清单中所有标记为"需生成场景日志"的调用链是否都有对应的 `{场景名}_场景日志.md` 文档

#### Scenario: Missing module-level log doc triggers generation
- **WHEN** 日志触发模块缺少模块级核心日志文档
- **THEN** 系统 SHALL 标记缺失 → 触发生成 → 重新自检

#### Scenario: Missing function-level log doc triggers generation
- **WHEN** 标记为需生成的函数缺少函数级日志文档
- **THEN** 系统 SHALL 标记缺失 → 触发生成 → 重新自检

#### Scenario: Missing scenario-level log doc triggers generation
- **WHEN** 标记为需生成的场景缺少场景日志文档
- **THEN** 系统 SHALL 标记缺失 → 触发生成 → 重新自检

#### Scenario: Manual mode annotation check
- **WHEN** 自检 Step 5 执行且 codegraph 不可用
- **THEN** 系统 SHALL 检查所有日志文档头部是否包含 `[手动模式]` 标注，缺失则补充

#### Scenario: Architecture change triggers log sync check
- **WHEN** 架构文档有变更（模块新增/更新）
- **THEN** 系统 SHALL 检查对应模块的日志触发状态是否更新，并确保所有层次日志同步更新

## ADDED Requirements

### Requirement: Two-layer log completeness ratio in self-check report
自检报告 SHALL 包含两层日志的完整性统计。

#### Scenario: Self-check report includes two-layer log stats
- **WHEN** 自检报告生成
- **THEN** 系统 SHALL 输出两层日志统计：① 第一层模块级日志：应生成 N 个 / 已生成 M 个 / 覆盖率 M/N × 100%、② 第二层函数级日志：应生成 X 个 / 已生成 Y 个 / 覆盖率 Y/X × 100%、③ 第二层场景日志：应生成 P 个 / 已生成 Q 个 / 覆盖率 Q/P × 100%

#### Scenario: Manual mode disables second-layer stats
- **WHEN** 自检报告生成且 codegraph 不可用
- **THEN** 系统 SHALL 在第二层统计处显示 "N/A（codegraph 不可用，第二层日志未生成）" 并标注说明
