## ADDED Requirements

### Requirement: Annotate entry trigger type in all sequence diagrams
所有时序图 SHALL 在首步标注入口的触发类型（UI/系统/外部事件）。

#### Scenario: System entry annotation format
- **WHEN** 时序图描画系统触发流程
- **THEN** 系统 SHALL 在首步使用 `Note over` 标注触发条件，格式：`Note over <参与者>: 系统触发: <条件>`

#### Scenario: External event annotation format
- **WHEN** 时序图描画外部事件流程
- **THEN** 系统 SHALL 在首步使用 `Note over` 标注事件来源，格式：`Note over <参与者>: 外部事件: <来源>`
