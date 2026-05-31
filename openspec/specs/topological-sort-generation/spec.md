## ADDED Requirements

### Requirement: Topological sort modules by dependency depth
系统 SHALL 在扫描阶段对模块进行拓扑排序，按依赖深度分组。

#### Scenario: Topological sort groups modules into layers
- **WHEN** 依赖矩阵提取完成（模块 A→C, B→C, C→D, D→无依赖）
- **THEN** 系统 SHALL 输出分组结果：Layer 0: [D], Layer 1: [C], Layer 2: [A, B]，同层模块无相互依赖可并行生成

#### Scenario: Circular dependency merged into single layer
- **WHEN** 模块 A 依赖 B、模块 B 依赖 A 形成循环
- **THEN** 系统 SHALL 将 A 和 B 合并为同一虚拟层，同层并行生成，并在分组报告中标注循环路径

#### Scenario: Multi-node circular dependency handling
- **WHEN** 模块 A→B→C→A 形成 3 节点循环
- **THEN** 系统 SHALL 将 A、B、C 合并为同一层，层内标注为循环依赖组

### Requirement: Generate module documents in topological order
系统 SHALL 按拓扑排序的层级顺序生成模块文档，同层模块可并行生成。

#### Scenario: Sequential layer generation
- **WHEN** 拓扑排序结果为 Layer 0 → Layer 1 → Layer 2
- **THEN** 系统 SHALL 先生成 Layer 0 的所有模块文档，完成后生成 Layer 1，最后生成 Layer 2

#### Scenario: Same-layer modules can be generated in any order
- **WHEN** Layer 1 包含 [featureA, featureB] 两个互不依赖的模块
- **THEN** 系统 SHALL 生成 featureA 和 featureB 的模块文档，生成顺序不影响正确性

#### Scenario: Downstream module references upstream docs
- **WHEN** 生成 Layer 1 的模块 B 文档，B 依赖 Layer 0 的模块 A
- **THEN** 系统 SHALL 在模块 B 的第3章依赖关系中引用已生成的模块 A 架构文档内容
