## Why

当前生成阶段中，Agent 按拓扑排序逐个生成模块文档，但没有进度追踪机制。一旦 Agent 被中断（上下文溢出、网络异常、用户暂停），CP0 增量检测无法区分"部分生成但被中断"和"全部生成完成"，导致需要人工判断恢复点或被迫全量重做。同时，单个模块文档的生成失败没有重试机制，失败后直接跳过整个模块，浪费已生成内容。

## What Changes

- **NEW**: 生成阶段前创建章节级任务清单 `.task-checklist.md`，持久化到 `docs/architecture/`
- **NEW**: 任务清单含 5 种状态：`[ ]` 未开始、`[>]` 进行中、`[1]` 失败待重试、`[x]` 完成、`[!]` 跳过
- **NEW**: 失败重试机制：首次失败标记 `[1]` 自动重试一次，再次失败标记 `[!]` 跳过并记录原因
- **NEW**: 中断恢复机制：CP0 检测未完成任务清单 → 推荐"恢复执行" → 从第一个非完成非跳过任务继续
- **NEW**: CP2.5 与任务清单联动：Step 1 模块覆盖检查对比任务清单预期 vs 实际完成

## Capabilities

### New Capabilities

- `task-checklist-management`: 章节级任务清单的生成、状态转换、持久化。按策略（A/B/C/D）和拓扑排序生成按章节划分的任务列表。支持 5 种状态转换。存储为 `docs/architecture/.task-checklist.md`

### Modified Capabilities

- `incremental-detection`: CP0 新增任务清单状态检测 — 检测 `.task-checklist.md` 是否存在且未完成，若存在则推荐"恢复执行"模式
- `architecture-doc-generation`: 生成阶段前新增"生成任务清单"步骤；逐项执行任务清单代替批量生成；任务完成时标记状态；失败重试逻辑
- `self-check-verification`: Step 1 模块覆盖检查追加任务清单比对 — 对比任务清单预期任务数 vs `[x]` 完成数，`[!]` 跳过项列为已知不完整

## Impact

- **SKILL.md**: 生成阶段新增任务清单生成步骤；CP0 新增恢复执行逻辑；CP2.5 Step 1 追加任务清单比对
- **references/quick-ref.md**: 策略步骤新增任务清单生成和逐项执行描述
- **references/self-check-spec.md**: Step 1 检查项追加任务清单覆盖率
- **references/doc-format.md**: 新增 `.task-checklist.md` 文件格式规范
- **docs/architecture/.task-checklist.md**: 新文件，持久化的任务清单
