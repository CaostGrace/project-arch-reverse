## ADDED Requirements

### Requirement: Load platform entry catalog by project type
系统 SHALL 根据 CP1 确认的项目类型加载对应的入口目录。

#### Scenario: Load Android entry catalog
- **WHEN** 项目类型确认为 Android
- **THEN** 系统 SHALL 从 `project-types-config.md` 加载 Android 的入口点列表（ContentProvider、Service、BroadcastReceiver、WorkManager、Application、Composable、Notification 等）

#### Scenario: Load iOS entry catalog
- **WHEN** 项目类型确认为 iOS
- **THEN** 系统 SHALL 加载 iOS 入口（AppDelegate、SceneDelegate、URL Scheme、Push Notification、Background Fetch、WidgetKit 等）

#### Scenario: Mixed project loads each sub-type catalog
- **WHEN** 项目为混合类型
- **THEN** 系统 SHALL 对主类型和子类型分别加载各自的入口目录

### Requirement: Scan source files for entry points
系统 SHALL 在扫描阶段对每个模块的源码执行入口模式 grep 搜索。

#### Scenario: Grep for direct entry points
- **WHEN** 入口类型为"直接入口"（如 extends ContentProvider）
- **THEN** 系统 SHALL 使用正则搜索类声明行（如 `extends\s+ContentProvider`），排除注释行

#### Scenario: Grep for indirect entry points
- **WHEN** 入口类型为"间接入口"（如 @Scheduled、@KafkaListener）
- **THEN** 系统 SHALL 搜索注解+方法签名组合，提取方法和代码位置

#### Scenario: Output entry point inventory
- **WHEN** 所有模块扫描完毕
- **THEN** 系统 SHALL 输出入口清单：模块名 | 入口方法 | 文件:行号 | 入口类型（直接/间接）| 触发类型（UI/系统/外部）

### Requirement: Annotate entry trigger type in sequence diagrams
系统 SHALL 在时序图中标注入口的触发类型和条件。

#### Scenario: System-triggered entry annotation
- **WHEN** 时序图起点为系统触发入口（如 WorkManager.doWork）
- **THEN** 系统 SHALL 在时序图首步添加 `Note over <参与方>: 系统触发: <触发条件>`

#### Scenario: UI-triggered entry annotation
- **WHEN** 时序图起点为 UI 触发入口（如 onClick）
- **THEN** 系统 SHALL 标注 `Note over UI: UI触发: <交互描述>`

#### Scenario: External event entry annotation
- **WHEN** 时序图起点为外部事件入口（如 @KafkaListener）
- **THEN** 系统 SHALL 标注 `Note over <参与方>: 外部事件: <事件描述>`

### Requirement: Trace entry points across module boundaries
系统 SHALL 在跨模块分析中从系统入口追踪到最终终点。

#### Scenario: Cross-module flow starts from entry
- **WHEN** 跨模块流程涉及系统入口
- **THEN** 系统 SHALL 从入口所在模块开始绘制时序，贯穿所有涉及的模块直到最终数据层

#### Scenario: Entry module is in app layer
- **WHEN** 入口在 app 模块，下游调用跨 order 和 payment 模块
- **THEN** 系统 SHALL 在 §12.4 跨模块时序图中包含 app 模块的入口参与者，box 分组标注各模块
