# 项目类型流程配置

本文件包含各项目类型的检测标准、特定章节和分析流程，供 SKILL.md 主文档引用。

## 项目类型检测配置

| 项目类型 | 检测标准 | 配置文件 |
|----------|----------|----------|
| Android | `build.gradle`、`AndroidManifest.xml`、`.kt` 文件 | `build.gradle.kts`, `settings.gradle.kts` |
| iOS | `.xcworkspace`、`.xcodeproj`、`.swift` 文件 | `Podfile`, `Package.swift` |
| Java 后端 | `pom.xml`、`build.gradle`、Spring Boot 模式 | `pom.xml`, `application.yml` |
| Vue.js | `package.json` 含 vue、`.vue` 文件 | `package.json`, `vite.config.ts` |
| React | `package.json` 含 react、`.jsx`/`.tsx` 文件 | `package.json`, `next.config.js` |
| Flutter | `pubspec.yaml`、`.dart` 文件 | `pubspec.yaml` |
| Node.js | `package.json`、`.js`/`.ts` 后端文件 | `package.json`, `server.js` |
| HarmonyOS Next | `hvigorfile.ts`、`oh-package.json5`、`.ets` 文件、AppScope 目录 | `app.json5`, `module.json5`, `hvigorfile.ts` |

## 各项目类型特定章节

### Android 项目
- 技术栈：Kotlin/Jetpack Compose/Hilt等
- 架构模式：MVVM/Clean Architecture
- 数据库：Room实体、ER图
- 循环依赖检测：使用Gradle `dependencyInsight`分析
- 模块分类：应用层(app)、功能层(feature:*)、核心层(core:*)、构建逻辑(build-logic)

**特有分析流程**：
1. 读取 `settings.gradle.kts` 分析模块间依赖
2. 使用 Gradle `dependencyInsight` 检测循环依赖
3. 基于检测结果生成依赖优化建议

### iOS 项目
- 技术栈：Swift/SwiftUI/UIKit等
- 架构模式：MVVM/Clean Architecture
- 本地存储：Core Data/Realm
- 依赖管理：SPM/CocoaPods/Carthage
- 状态管理方案

### Java 后端项目
- 技术栈：Java/Spring Boot等
- 架构模式：三层架构/Clean Architecture/微服务
- API端点分析
- 业务逻辑层设计
- 缓存策略：Redis等
- 消息队列设计
- 性能与容量规划

### Vue.js 前端项目
- 技术栈：Vue.js/Vite等
- 架构模式：Composition API/Options API
- 路由结构
- 状态管理：Pinia/Vuex
- 组件层次结构：Atomic Design
- 样式规范

### React 前端项目
- 技术栈：React/Next.js等
- 架构模式：Hooks/Class Component
- 路由结构
- 状态管理：Redux/Zustand/Recoil
- 样式方案：CSS Modules/Styled Components/Tailwind

### Flutter 项目
- 技术栈：Flutter/Dart等
- 架构模式：BLoC/Provider/Riverpod
- 页面导航结构
- 状态管理方案
- 网络层设计
- 本地存储设计
- 平台特定代码

### Node.js 后端项目
- 技术栈：Node.js/Express/NestJS等
- 架构模式：MVC/三层架构/微服务
- API端点分析
- 中间件配置
- 缓存策略
- 日志方案
- 消息队列设计
- 监控与可观测性

### HarmonyOS Next 项目
- 技术栈：ArkTS/ArkUI/HarmonyOS API等
- 架构模式：三层架构（共用层+特性层+产品定制层）
- 模块形态：HAP/HAR/HSP包类型选择
- 分布式能力：分布式软总线、跨设备虚拟化
- 设备形态适配：手机/平板/折叠屏/PC/大屏/手表

**特有分析流程**：
1. 工程结构分析：AppScope目录、Module目录结构
2. 包类型分析：HAP（动态加载）、HAR（静态共享）、HSP（动态共享）
3. 三层架构分析：共用层 → 特性层 → 产品定制层（单向依赖）
4. 分布式能力分析：分布式软总线、分布式数据管理、分布式调度
5. 配置文件分析：app.json5、module.json5、hvigorfile.ts、oh-package.json5
6. 资源管理分析：AppScope资源与Module资源的优先级处理
7. 形态适配分析：响应式布局、窗口模式、交互适配

## 分析流程映射

| 项目类型 | 依赖分析方法 | 循环依赖检测 | 特有分析项 |
|----------|--------------|--------------|------------|
| Android | 解析 settings.gradle.kts | Gradle dependencyInsight | 模块分类分析 |
| iOS | 解析 Package.swift/Podfile | SPM/CocoaPods分析 | 依赖管理分析 |
| Java 后端 | 解析 pom.xml/build.gradle | Maven/Gradle分析 | API端点分析 |
| Vue.js | 解析 package.json | ES Module分析 | 组件层次分析 |
| React | 解析 package.json | ES Module分析 | 组件层次分析 |
| Flutter | 解析 pubspec.yaml | Dart分析 | 平台特定代码分析 |
| Node.js | 解析 package.json | ES Module分析 | 中间件分析 |
| HarmonyOS Next | 解析 oh-package.json5/hvigorfile.ts | HAR/HSP检测 | 分布式能力分析 |

## 编译指南配置

各项目类型的编译目标、用途和命令详见独立文件：

| 项目类型 | 编译指南文件 |
|----------|--------------|
| Android | build-guide-android.md |
| iOS | build-guide-ios.md |
| Java 后端 | build-guide-java-backend.md |
| Vue.js | build-guide-vue.md |
| React | build-guide-react.md |
| Flutter | build-guide-flutter.md |
| Node.js | build-guide-nodejs.md |
| HarmonyOS Next | build-guide-harmonyos.md |

## 文档模板映射

| 项目类型 | 根文档模板 | 模块文档模板 |
|----------|------------|--------------|
| Android | android-template.md | android-module-template.md |
| iOS | ios-template.md | ios-module-template.md |
| Java 后端 | java-backend-template.md | java-module-template.md |
| Vue.js | vue-template.md | vue-module-template.md |
| React | react-template.md | react-module-template.md |
| Flutter | flutter-template.md | flutter-module-template.md |
| Node.js | nodejs-template.md | nodejs-module-template.md |
| HarmonyOS Next | harmonyos-template.md | harmonyos-module-template.md |

## 异常处理映射

| 项目类型 | 特有异常场景 | 处理策略 |
|----------|--------------|----------|
| Android | Gradle配置解析失败 | 使用目录结构推断模块关系 |
| iOS | Podfile解析失败 | 使用SPM作为备选 |
| Java 后端 | pom.xml解析失败 | 使用目录结构推断模块 |
| Vue.js | vite.config解析失败 | 使用默认配置 |
| React | next.config解析失败 | 使用默认路由配置 |
| Flutter | pubspec.yaml解析失败 | 使用目录结构推断 |
| Node.js | package.json解析失败 | 使用目录结构推断 |
| HarmonyOS Next | AppScope缺失 | 提示用户确认项目完整性 |
