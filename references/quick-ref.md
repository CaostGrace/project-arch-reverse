# 项目架构逆向快速参考

本文件提供常用信息的快速查询，适用于执行时的快速参考。

## 快速导航

| 资源 | 说明 |
|------|------|
| [project-types-config.md](project-types-config.md) | 项目类型检测配置、分析流程 |
| [build-guide-android.md](build-guide-android.md) | Android 编译指南 |
| [build-guide-ios.md](build-guide-ios.md) | iOS 编译指南 |
| [build-guide-vue.md](build-guide-vue.md) | Vue.js 编译指南 |
| [build-guide-react.md](build-guide-react.md) | React 编译指南 |
| [build-guide-flutter.md](build-guide-flutter.md) | Flutter 编译指南 |
| [build-guide-java-backend.md](build-guide-java-backend.md) | Java 后端编译指南 |
| [build-guide-nodejs.md](build-guide-nodejs.md) | Node.js 编译指南 |
| [build-guide-harmonyos.md](build-guide-harmonyos.md) | HarmonyOS Next 编译指南 |

## 文档章节速查

### 根文档（21章）
1. 文档修订历史 → 2. 项目架构概览 → 3. 项目简介 → 4. 术语与缩略语 → 5. 技术栈 → 6. 模块依赖关系图 → 7. 依赖关系分析 → 8. 模块简略介绍 → 9. 主要组件功能介绍 → 10. 主页面架构 → 11. 页面跳转详情 → 12. 主要业务流程图 → 13. 数据库设计 → 14. 核心数据类关系图 → 15. 架构决策记录 → 16. 接口设计 → 17. 部署与运维方案 → 18. 安全设计 → 19. 风险与应对措施 → 20. 模块文档索引 → 21. 项目编译指南

### 模块文档（11章）
1. 模块概述 → 2. 依赖关系 → 3. 业务流程 → 4. 数据流 → 5. 核心功能流程图 → 6. 数据依赖关系 → 7. 依赖场景说明 → 8. 页面与路由 → 9. 导航调用示例 → 10. 关键类和方法 → 11. 测试策略

## 执行策略选择

| 策略 | 条件 | 操作 |
|------|------|------|
| A 全量生成 | docs/ + 所有模块docs/ 均不存在 | 创建全部文档 |
| B 补充生成 | 根文档存在，模块文档部分缺失 | 生成缺失模块 |
| C 增量更新 | 根文档 + 所有模块文档均存在 | 对比更新 |
| D 重建根文档 | 根文档不存在，模块文档存在 | 重建根文档 |

## 增量检测命令

```bash
# 检查文档存在性
ls docs/*.项目架构文档.md 2>/dev/null || echo "根文档不存在"
find . -path "*/docs/*模块架构.md" 2>/dev/null | head -20

# Android循环依赖检测
./gradlew dependencyInsight --configuration releaseRuntimeClasspath --dependency :core:data
```
