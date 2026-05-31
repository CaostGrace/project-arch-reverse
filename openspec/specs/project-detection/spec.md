## ADDED Requirements

### Requirement: Auto-detect project type from build files
系统 SHALL 通过扫描项目根目录及子目录（深度≤5）的构建配置文件，自动检测项目类型。

#### Scenario: Detect Android project
- **WHEN** 项目包含 `build.gradle` 或 `build.gradle.kts` 文件，且包含 `AndroidManifest.xml` 或 `.kt`/`.java` 文件
- **THEN** 系统 SHALL 识别项目类型为 "Android" 并加载 `references/android-template.md` 模板

#### Scenario: Detect iOS project
- **WHEN** 项目包含 `.xcworkspace` 或 `.xcodeproj` 目录，且包含 `.swift` 文件
- **THEN** 系统 SHALL 识别项目类型为 "iOS" 并加载 `references/ios-template.md` 模板

#### Scenario: Detect Java backend project
- **WHEN** 项目包含 `pom.xml` 或 `build.gradle`，且包含 Spring Boot 注解或 `application.yml` 文件
- **THEN** 系统 SHALL 识别项目类型为 "Java 后端" 并加载 `references/java-backend-template.md` 模板

#### Scenario: Detect Vue.js project
- **WHEN** 项目 `package.json` 的 dependencies 含 `vue`，且包含 `.vue` 文件
- **THEN** 系统 SHALL 识别项目类型为 "Vue.js" 并加载 `references/vue-template.md` 模板

#### Scenario: Detect React project
- **WHEN** 项目 `package.json` 的 dependencies 含 `react`，且包含 `.jsx`/`.tsx` 文件
- **THEN** 系统 SHALL 识别项目类型为 "React" 并加载 `references/react-template.md` 模板

#### Scenario: Detect Flutter project
- **WHEN** 项目包含 `pubspec.yaml` 文件且包含 `.dart` 文件
- **THEN** 系统 SHALL 识别项目类型为 "Flutter" 并加载 `references/flutter-template.md` 模板

#### Scenario: Detect Node.js project
- **WHEN** 项目 `package.json` 未检测到 Vue/React 依赖，但包含 `server.js` 或后端框架（Express/NestJS）特征
- **THEN** 系统 SHALL 识别项目类型为 "Node.js" 并加载 `references/nodejs-template.md` 模板

#### Scenario: Detect HarmonyOS Next project
- **WHEN** 项目包含 `hvigorfile.ts`、`oh-package.json5`、`.ets` 文件或 `AppScope` 目录
- **THEN** 系统 SHALL 识别项目类型为 "HarmonyOS Next" 并加载 `references/harmonyos-template.md` 模板

### Requirement: Handle mixed project types
当项目同时匹配多种类型签名时，系统 SHALL 识别主类型和子类型并分别处理。

#### Scenario: Android with Flutter plugin
- **WHEN** 项目同时包含 `build.gradle.kts` 和子目录中的 `pubspec.yaml`
- **THEN** 系统 SHALL 将 Android 识别为主类型，Flutter 插件部分识别为子类型，分别使用对应模板

#### Scenario: Monorepo with frontend and backend
- **WHEN** 顶层包含 `package.json`，子目录包含 `pom.xml` 或独立的 `package.json`（Express/NestJS）
- **THEN** 系统 SHALL 按模块数量多的类型为主类型，每个包独立生成文档

#### Scenario: React Native with native code
- **WHEN** `package.json` 含 `react-native` 依赖，且存在 `android/` 和 `ios/` 目录
- **THEN** 系统 SHALL 将 React Native 识别为主类型，原生部分识别为子模块

### Requirement: Allow user to override detected type
用户 SHALL 能够在 CP0/CP1 阶段手动指定项目类型，覆盖自动检测结果。

#### Scenario: User specifies different type
- **WHEN** 用户在 CP0 或 CP1 阶段指定项目类型（如 "Android" 但自动检测为 "Flutter"）
- **THEN** 系统 SHALL 以用户指定为准，使用指定类型的模板和配置，忽略自动检测结果

#### Scenario: Build file parsing fails
- **WHEN** 构建文件解析失败（如 Gradle 配置错误）
- **THEN** 系统 SHALL 降级为目录结构推断项目类型，并发出警告

### Requirement: Determine main project type in mixed projects
系统 SHALL 统计各类型匹配的文件/模块数量来确定主类型。

#### Scenario: Multiple type signatures detected
- **WHEN** 项目匹配多种项目类型签名
- **THEN** 系统 SHALL 统计各类型匹配的文件/模块数量，以最多匹配的类型为主类型，列出所有子类型及其模块范围

### Requirement: Use type-specific analysis flows
系统 SHALL 根据项目类型应用特有的分析流程。

#### Scenario: Android-specific analysis
- **WHEN** 项目类型为 Android
- **THEN** 系统 SHALL 读取 `settings.gradle.kts` 分析模块间依赖，使用 Gradle `dependencyInsight` 检测循环依赖

#### Scenario: HarmonyOS-specific analysis
- **WHEN** 项目类型为 HarmonyOS Next
- **THEN** 系统 SHALL 分析 AppScope 目录结构、HAP/HAR/HSP 包类型、三层架构（共用层→特性层→产品定制层）单向依赖
