## ADDED Requirements

### Requirement: Generate platform-specific log output examples
系统 SHALL 为每条提取到的日志语句生成该平台对应的实际输出示例。

#### Scenario: Android log output example
- **WHEN** 项目类型为 Android 且使用 `Log.i(TAG, "message, param=%d", value)`
- **THEN** 系统 SHALL 生成示例：`MM-DD HH:MM:SS.mmm PID TID I TAG: message, param={value}`

#### Scenario: iOS log output example
- **WHEN** 项目类型为 iOS 且使用 `Logger.info("message, param=\(value)")`
- **THEN** 系统 SHALL 生成示例：`YYYY-MM-DD HH:MM:SS.mmm+TZ App[PID:TID] [TAG] message, param={value}`

#### Scenario: Java backend log output example
- **WHEN** 项目类型为 Java 后端且使用 `log.info("message, param={}", value)`
- **THEN** 系统 SHALL 生成示例：`YYYY-MM-DD HH:MM:SS.mmm [thread] INFO logger - message, param={value}`

#### Scenario: Node.js log output example
- **WHEN** 项目类型为 Node.js 且使用 `logger.info("message", {param: value})`
- **THEN** 系统 SHALL 生成示例：`YYYY-MM-DDTHH:MM:SS.mmmZ [info]: message {"param":"{value}"}`

#### Scenario: Flutter log output example
- **WHEN** 项目类型为 Flutter 且使用 `debugPrint("message $value")`
- **THEN** 系统 SHALL 生成示例：`[TAG] message {value}`

#### Scenario: HarmonyOS log output example
- **WHEN** 项目类型为 HarmonyOS Next 且使用 `hilog.info(domain, TAG, "message %{public}d", value)`
- **THEN** 系统 SHALL 生成示例：`MM-DD HH:MM:SS.mmm PID TID I domain/TAG: message {value}`

### Requirement: Generate grep-ready troubleshooting commands
系统 SHALL 为每条日志和每个排查场景生成可直接复制的 grep/filter 命令。

#### Scenario: Generate grep command for each log entry
- **WHEN** 输出 §6 日志清单
- **THEN** 系统 SHALL 在"grep命令"列生成平台对应的过滤命令，如 Android: `adb logcat | grep "TAG" | grep "keyword"`

#### Scenario: Generate step-by-step troubleshooting commands in §8
- **WHEN** 生成 §8 故障排查决策树
- **THEN** 系统 SHALL 每个排查步骤附带：① grep 命令 ② 预期输出示例 ③ 无输出时的诊断方向

#### Scenario: Command includes expected output and fallback
- **WHEN** 输出排查步骤
- **THEN** 系统 SHALL 标注"预期输出"和"⚠️ 无输出 → 检查 XXX"的 fallback 诊断

### Requirement: Platform-specific log format reference in §7
系统 SHALL 在模块级日志 §7 中提供项目类型对应的日志格式速查。

#### Scenario: §7 includes platform log format reference
- **WHEN** 生成模块级日志 §7
- **THEN** 系统 SHALL 包含"本平台日志格式"小节：时间戳格式说明、TAG/Logger名位置、PID/TID含义、默认过滤命令
