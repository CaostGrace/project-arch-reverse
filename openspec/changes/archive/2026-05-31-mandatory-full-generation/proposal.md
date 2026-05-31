## Why

SKILL.md 多处声明"所有文档必须生成，不可省略"，但实际执行中 AI 能找到后门绕过去：任务清单的 `[!]` 状态让"跳过"成为合法行为、CP6 的"用户主动确认接受残留"让文档缺失可以靠用户点头退出、执行指令中缺乏显式禁止"询问用户是否继续"的约束。结果：生成 2 篇日志后输出"基础完成，21 个核心模块可后续补充"。必须消除所有让 AI 可以"合法不生成"的后门，把强制生成变成铁律。

## What Changes

- **BREAKING**: 收紧任务清单 `[!]` 跳过状态语义——仅限不可恢复错误（源码删除、编码异常），禁止因"数量多"使用
- **BREAKING**: CP4 增加"生成阶段铁律"——5 条禁止行为清单，严禁询问用户是否继续、严禁"后续补充"措辞、严禁"基础完成"标注
- **BREAKING**: CP6 终止条件收紧——文档缺失类问题不可通过"用户接受残留"退出，必须修复
- **修改** CP4 任务清单机制：`[!]` 触发条件从"失败"收紧为"不可恢复错误"
- **修改** CP6 判定逻辑：区分"可自动修复(文档缺失)"和"不可自动修复(领域知识)"，前者不允许退出
- **新增** 全局禁止行为清单：放入 SKILL.md 资源使用原则或 CP0 检测章节

## Capabilities

### Modified Capabilities

- `task-checklist-management`: `[!]` 状态语义收紧，仅限不可恢复错误
- `self-check-verification`: CP6 终止条件区分可自动修复和不可自动修复，文档缺失禁止退出
- `incremental-detection`: CP4 增加强制生成铁律和禁止行为清单

## Impact

- SKILL.md: CP4 增加铁律（+5 条禁止行为），CP6 终止条件收紧，任务清单 `[!]` 语义更新
- references/doc-format.md: 任务清单 `[!]` 状态说明更新
- references/self-check-spec.md: CP6 终止条件更新
- openspec/specs/task-checklist-management/: `[!]` 语义更新
- openspec/specs/self-check-verification/: 终止条件更新
