## ADDED Requirements

### Requirement: Output topological sort grouping
系统 SHALL 在依赖分析完成后输出拓扑排序分组结果，供生成阶段使用。

#### Scenario: Output layer-by-layer module groups
- **WHEN** 依赖矩阵和循环依赖检测完成
- **THEN** 系统 SHALL 使用 Kahn 算法输出按依赖深度分层的模块分组：Layer 0（无依赖模块）, Layer 1, Layer 2, ...，同层模块无相互依赖

#### Scenario: Annotate circular dependency groups
- **WHEN** 存在循环依赖的模块组
- **THEN** 系统 SHALL 将循环依赖组标注为"循环依赖组-{ID}"，输出为虚拟层，包含组内所有模块

#### Scenario: Grouping output format
- **WHEN** 输出拓扑排序结果
- **THEN** 系统 SHALL 输出格式：层级编号 | 模块列表 | 依赖层 | 备注（循环依赖标注）
