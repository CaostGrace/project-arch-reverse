# 项目类型流程配置

本文件包含各项目类型的检测标准、特定章节和分析流程，供 SKILL.md 主文档引用。

## 项目类型检测配置

| 项目类型 | 检测标准 | 配置文件 | codegraph 支持 |
|----------|----------|----------|:---:|
| Android | `build.gradle`、`AndroidManifest.xml`、`.kt` 文件 | `build.gradle.kts`, `settings.gradle.kts` | ✅ Kotlin/Java |
| iOS | `.xcworkspace`、`.xcodeproj`、`.swift` 文件 | `Podfile`, `Package.swift` | ✅ Swift |
| Java 后端 | `pom.xml`、`build.gradle`、Spring Boot 模式 | `pom.xml`, `application.yml` | ✅ Java |
| Vue.js | `package.json` 含 vue、`.vue` 文件 | `package.json`, `vite.config.ts` | ✅ TypeScript/JS |
| React | `package.json` 含 react、`.jsx`/`.tsx` 文件 | `package.json`, `next.config.js` | ✅ TypeScript/JS |
| Flutter | `pubspec.yaml`、`.dart` 文件 | `pubspec.yaml` | ✅ Dart |
| Node.js | `package.json`、`.js`/`.ts` 后端文件 | `package.json`, `server.js` | ✅ TypeScript/JS |
| HarmonyOS Next | `hvigorfile.ts`、`oh-package.json5`、`.ets` 文件、AppScope 目录 | `app.json5`, `module.json5`, `hvigorfile.ts` | ⚠️ ArkTS (降级测试) |

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

## 核心功能入口点 ★

> 每种项目类型定义系统级入口目录，用于扫描阶段发现核心功能的真正起点。入口分为**直接入口**（类声明匹配）和**间接入口**（注解/API/生命周期钩子），按**触发类型**分为 UI触发、系统触发、外部事件三种。
>
> 条目格式：`入口名 | 搜索模式(grep) | 入口类型 | 触发类型 | 分析行为`

### Android — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| ContentProvider | `extends\s+ContentProvider` | 直接 | 外部事件 | 从 query/insert/update/delete 向下追踪到数据层 |
| Service | `extends\s+(Intent)?Service` | 直接 | 系统触发 | 从 onStartCommand/onBind/onHandleIntent 向下追踪 |
| BroadcastReceiver | `extends\s+BroadcastReceiver` | 直接 | 外部事件 | 从 onReceive(intent) 向下追踪处理链 |
| WorkManager Worker | `extends\s+Worker\|ListenableWorker` | 直接 | 系统触发 | 从 doWork() 向下追踪业务逻辑 |
| Application.onCreate | `extends\s+Application` | 直接 | 系统触发 | 从 onCreate() 追踪初始化链（DI/数据库/网络） |
| Activity | `extends\s+(App)?CompatActivity\|FragmentActivity` | 直接 | UI触发 | 从 onCreate/onResume/onNewIntent 追踪页面初始化 |
| Fragment | `extends\s+Fragment\|DialogFragment` | 直接 | UI触发 | 从 onViewCreated/onAttach 追踪 UI 绑定 |
| Compose @Composable | `@Composable\s+fun\s+\w+` | 间接 | UI触发 | 从 Composable 函数入口追踪状态和副作用 |
| ViewModel | `extends\s+ViewModel` | 直接 | UI触发 | 从 ViewModel 方法追踪到 Repository/UseCase |
| Notification | `NotificationManager\|PendingIntent` | 间接 | 系统触发 | 从 PendingIntent 追踪目标 Activity/Service |
| FCM/Firebase | `extends\s+FirebaseMessagingService` | 直接 | 外部事件 | 从 onMessageReceived 追踪推送处理 |

### iOS — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| AppDelegate | `UIApplicationDelegate` | 直接 | 系统触发 | 从 didFinishLaunching/handleOpenURL/didReceiveRemoteNotification 追踪 |
| SceneDelegate | `UIWindowSceneDelegate` | 直接 | 系统触发 | 从 scene(willConnectTo) 追踪场景初始化 |
| URL Scheme | `func\s+scene\(.*openURLContexts` | 间接 | 外部事件 | 从 URL 处理函数追踪路由分发 |
| Push Notification | `didReceiveRemoteNotification\|UNUserNotificationCenterDelegate` | 间接 | 外部事件 | 从推送回调追踪通知处理链 |
| Background Fetch | `performFetchWithCompletionHandler` | 间接 | 系统触发 | 从后台拉取回调追踪数据同步 |
| WidgetKit | `TimelineProvider\|Widget` | 直接 | 系统触发 | 从 getTimeline/snapshot 追踪数据源 |
| Siri Intent | `IntentHandler\|INIntent` | 直接 | 外部事件 | 从 handle(intent:) 追踪意图处理 |
| HealthKit | `HKHealthStore\|HKQuery` | 间接 | 外部事件 | 从 observer/sample 查询追踪健康数据 |
| SwiftUI .onAppear | `\.onAppear\s*\{` | 间接 | UI触发 | 从 onAppear 闭包追踪首屏数据加载 |
| @StateObject | `@StateObject\s+var\s+\w+` | 间接 | UI触发 | 从 StateObject 初始化追踪 ViewModel 绑定 |
| Combine sink | `\.sink\s*\{` | 间接 | 外部事件 | 从 sink 闭包追踪数据流订阅 |

### Java 后端 — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| @Scheduled | `@Scheduled\(` | 间接 | 系统触发 | 从定时方法追踪业务逻辑调用链 |
| @KafkaListener | `@KafkaListener\(` | 间接 | 外部事件 | 从消息处理方法追踪消费→处理→持久化 |
| @RabbitListener | `@RabbitListener\(` | 间接 | 外部事件 | 从队列监听方法追踪消息处理链 |
| @EventListener | `@EventListener\|ApplicationListener` | 间接 | 系统触发 | 从事件回调追踪事件处理→广播 |
| Filter | `implements\s+Filter` | 直接 | UI触发 | 从 doFilter 追踪请求预处理 |
| Interceptor | `extends\s+HandlerInterceptor` | 直接 | UI触发 | 从 preHandle/postHandle 追踪拦截处理 |
| @ControllerAdvice | `@ControllerAdvice\|@RestControllerAdvice` | 间接 | 系统触发 | 从 @ExceptionHandler 追踪异常处理 |
| Spring Batch | `implements\s+Tasklet\|ItemProcessor` | 直接 | 系统触发 | 从 execute/process 追踪批处理逻辑 |
| WebSocket | `@ServerEndpoint\|onOpen\|onMessage` | 间接 | 外部事件 | 从消息事件追踪推送/广播 |
| CommandLineRunner | `implements\s+CommandLineRunner` | 直接 | 系统触发 | 从 run() 追踪启动逻辑 |
| GraphQL Resolver | `@QueryMapping\|@SchemaMapping\|GraphQLResolver` | 间接 | UI触发 | 从 resolver 方法追踪数据获取链 |

### Vue.js — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| router.beforeEach | `beforeEach\(\(to,\s*from` | 间接 | UI触发 | 从路由守卫追踪鉴权/重定向逻辑 |
| Pinia store action | `defineStore\(.*\{` | 间接 | UI触发 | 从 store actions 追踪状态变更→副作用 |
| provide/inject | `provide\(.*\)` | 间接 | UI触发 | 从 provider 追踪依赖注入链 |
| mounted() | `mounted\(\)\s*\{` | 间接 | UI触发 | 从 mounted 钩子追踪首屏数据加载 |
| watch() | `watch\(.*\)` | 间接 | UI触发 | 从 watch 回调追踪响应式副作用 |
| computed | `computed\(\)` | 间接 | UI触发 | 从计算属性追踪派生状态 |
| onBeforeRouteUpdate | `onBeforeRouteUpdate\(` | 间接 | UI触发 | 从路由更新钩子追踪参数变更处理 |
| Nuxt plugins | `defineNuxtPlugin\(` | 间接 | 系统触发 | 从 plugin 入口追踪全局初始化 |
| useFetch/useAsyncData | `useFetch\(.*\)\|useAsyncData\(` | 间接 | UI触发 | 从数据获取调用追踪网络请求→渲染 |

### React — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| useEffect | `useEffect\(\(\s*\)\s*=>` | 间接 | UI触发 | 从 effect 追踪副作用（数据加载/订阅） |
| useState | `useState\(` | 间接 | UI触发 | 从 state 初始化追踪默认值来源 |
| useContext | `useContext\(` | 间接 | UI触发 | 从 context 消费追踪 Provider 数据源 |
| useReducer | `useReducer\(` | 间接 | UI触发 | 从 reducer 追踪状态流转 |
| React Router loader | `loader\s*:\s*(async\s*)?\(` | 间接 | UI触发 | 从 loader 追踪路由级数据预取 |
| React Router action | `action\s*:\s*(async\s*)?\(` | 间接 | UI触发 | 从 action 追踪表单提交处理 |
| Next.js getServerSideProps | `getServerSideProps` | 间接 | 系统触发 | 从 SSR 函数追踪服务端数据获取 |
| Next.js middleware | `middleware\.ts\|middleware\.js` | 间接 | UI触发 | 从 middleware 追踪请求拦截 |
| createRoot/render | `createRoot\|ReactDOM\.render` | 直接 | 系统触发 | 从入口渲染追踪组件树根 |

### Flutter — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| MethodChannel handler | `MethodChannel\(.*\).*setMethodCallHandler` | 间接 | 外部事件 | 从平台通道回调追踪原生通信 |
| StreamBuilder | `StreamBuilder\(` | 间接 | UI触发 | 从 stream builder 追踪数据流订阅 |
| BLoC BlocBuilder | `BlocBuilder<` | 间接 | UI触发 | 从 bloc builder 追踪状态渲染 |
| BLoC BlocListener | `BlocListener<` | 间接 | 系统触发 | 从 bloc listener 追踪一次性副作用 |
| Provider / Riverpod | `Provider<\|Consumer<\|ref.watch\|ref.read` | 间接 | UI触发 | 从 provider 引用追踪依赖注入 |
| initState | `void\s+initState\(\)\s*\{` | 间接 | UI触发 | 从 initState 追踪 Widget 初始化 |
| didChangeDependencies | `void\s+didChangeDependencies` | 间接 | UI触发 | 从依赖变更钩子追踪 InheritedWidget |
| Workmanager callback | `callbackDispatcher\|Workmanager\(\)` | 间接 | 系统触发 | 从后台任务回调追踪离线处理 |
| push notification | `FirebaseMessaging\.onMessage\|FlutterLocalNotificationsPlugin` | 间接 | 外部事件 | 从推送回调追踪通知处理 |

### Node.js — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| Express middleware | `app\.use\(` | 间接 | UI触发 | 从中间件追踪请求预处理/鉴权 |
| cron.schedule | `cron\.schedule\(` | 间接 | 系统触发 | 从定时任务追踪周期性处理 |
| queue.process | `queue\.process\(` | 间接 | 外部事件 | 从队列处理器追踪消息消费 |
| ws.on('message') | `\.on\(\s*['"]message['"]` | 间接 | 外部事件 | 从 WebSocket 消息追踪实时通信 |
| EventEmitter.on | `\.on\(\s*['"]\w+['"]\s*,` | 间接 | 外部事件 | 从事件监听追踪事件驱动流程 |
| worker_threads | `Worker\(|new\s+Worker\(` | 间接 | 系统触发 | 从 worker 线程追踪并行任务 |
| Express route handler | `\.(get|post|put|delete)\(` | 间接 | UI触发 | 从路由处理追踪 API 逻辑 |
| NestJS @Controller | `@Controller\(` | 间接 | UI触发 | 从控制器方法追踪路由处理 |
| Fastify hook | `fastify\.(addHook|register)` | 间接 | 系统触发 | 从 hook 追踪生命周期插件 |

### HarmonyOS Next — 核心功能入口点

| 入口 | 搜索模式 | 类型 | 触发类型 | 分析行为 |
|------|---------|------|---------|----------|
| ServiceAbility | `extends\s+ServiceAbility` | 直接 | 系统触发 | 从 onStart/onCommand 追踪后台服务 |
| DataAbility | `extends\s+DataAbility` | 直接 | 外部事件 | 从 query/insert/update/delete 追踪数据操作 |
| UIAbility | `extends\s+UIAbility` | 直接 | UI触发 | 从 onCreate/onForeground 追踪页面入口 |
| CommonEvent subscriber | `CommonEventManager\.subscribe\|CommonEventData` | 间接 | 外部事件 | 从订阅回调追踪系统事件处理 |
| BackgroundTaskManager | `BackgroundTaskManager\.startBackgroundRunning` | 间接 | 系统触发 | 从后台任务追踪长时运行 |
| @Entry @Component | `@Entry\s*@Component\|@Entry\s*struct` | 间接 | UI触发 | 从 @Entry 组件追踪页面入口 |
| aboutToAppear | `aboutToAppear\(\)\s*\{` | 间接 | UI触发 | 从生命周期钩子追踪组件初始化 |
| onPageShow | `onPageShow\(\)\s*\{` | 间接 | UI触发 | 从页面显示钩子追踪数据刷新 |
| Distributed scheduling | `DistributedSched\.startAbility\|distributedHardwareManager` | 间接 | 系统触发 | 从分布式调度追踪跨设备调用 |


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

## 混合项目类型检测 ★

当项目同时匹配多种类型签名时，按以下规则处理：

### 检测规则

| 场景 | 检测特征 | 主类型判定 |
|------|---------|-----------|
| Android + Flutter 插件 | `build.gradle.kts` + `pubspec.yaml`（在子目录） | Android 为主，Flutter 子模块 |
| Monorepo（前端+后端） | 顶层 `package.json` + 子目录含 `pom.xml`/`build.gradle` | 按模块数量多的类型为主 |
| React Native + 原生 | `package.json(含react-native)` + `android/` + `ios/` | React Native 为主，原生为子模块 |
| Node.js + Vue/React | 根 `package.json` + 子目录含 `.vue`/`.tsx` | 按入口文件判断（`server.js` vs `vite.config.*`） |

### 处理流程

```
1. 扫描所有构建文件签名 → 识别所有匹配的项目类型
2. 统计各类型匹配的文件/模块数量 → 确定主类型（最多匹配）
3. 列出子类型及其模块范围 → 用户确认
4. 分别加载对应类型的模板和配置
5. 生成文档时，根文档使用主类型模板，子模块使用各自类型模板
```

### 用户覆盖

用户可在 CP0/CP1 阶段手动指定项目类型，此时忽略自动检测结果，使用用户指定类型的模板和配置。

## 异常处理映射（原有）

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
