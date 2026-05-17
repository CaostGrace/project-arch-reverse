# Android 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| assembleDebug | 开发调试构建，包含调试信息，可直接安装到设备进行调试 | `./gradlew assembleDebug` | `app/build/outputs/apk/debug/` |
| assembleRelease | 正式发布构建，经过优化和混淆，需签名才能安装 | `./gradlew assembleRelease` | `app/build/outputs/apk/release/` |
| assemble{Variant} | 指定 flavors 和 build types 的组合构建 | `./gradlew assemble{Variant}` | `app/build/outputs/apk/{variant}/` |
| bundleDebug | 调试 bundle，用于本地测试 AAB格式 | `./gradlew bundleDebug` | `app/build/outputs/bundles/debug/` |
| bundleRelease | 发布 bundle，用于提交到应用市场 | `./gradlew bundleRelease` | `app/build/outputs/bundles/release/` |

## 常用组合

```bash
./gradlew assembleDemoDebug     # Demo版调试
./gradlew assembleProdDebug     # Prod版调试
./gradlew assembleDemoRelease   # Demo版发布
./gradlew assembleProdRelease   # Prod版发布
```

## 确认编译成功

```bash
./gradlew assembleDebug
# BUILD SUCCESSFUL 表示成功
```
