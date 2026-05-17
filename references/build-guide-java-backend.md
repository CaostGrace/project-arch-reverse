# Java 后端编译指南

## 编译目标（Maven）

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| package | 打包构建，生成可执行 JAR/WAR 文件 | `mvn package` | `target/*.jar` |
| package -DskipTests | 跳过测试打包，用于快速构建 | `mvn package -DskipTests` | `target/*.jar` |
| spring-boot:repackage | Spring Boot 可执行 JAR 打包 | `mvn spring-boot:repackage` | `target/*.jar` |
| war | 生成 WAR 包，用于部署到 Tomcat/Jetty | `mvn package -Dpackaging=war` | `target/*.war` |

## 编译目标（Gradle）

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| bootJar | Gradle 构建 Spring Boot 可执行 JAR | `./gradlew bootJar` | `build/libs/*.jar` |
| bootWar | Gradle 构建 WAR 包 | `./gradlew bootWar` | `build/libs/*.war` |
| build | 完整项目构建 | `./gradlew build` | `build/` |

## 确认编译成功

```bash
mvn package
# BUILD SUCCESS 表示成功
```
