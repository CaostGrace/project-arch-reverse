# Flutter 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| apk --debug | 调试 APK，直接安装到 Android 设备 | `flutter build apk --debug` | `build/app/outputs/flutter-apk/app-debug.apk` |
| apk --release | 发布 APK，经过优化和混淆 | `flutter build apk --release` | `build/app/outputs/flutter-apk/app-release.apk` |
| appbundle --debug | 调试 App Bundle | `flutter build app-bundle --debug` | `build/app/outputs/bundle/debug/` |
| appbundle --release | 发布 App Bundle | `flutter build app-bundle --release` | `build/app/outputs/bundle/release/` |
| ios --debug | iOS 模拟器构建 | `flutter build ios --debug` | `build/ios/iphonesimulator/` |
| ios --release | iOS 真机构建 | `flutter build ios --release` | `build/ios/iphoneos/` |
| web | Web 平台构建 | `flutter build web` | `build/web/` |

## 确认编译成功

```bash
flutter build apk --debug
# 输出 "Built build/app/outputs/flutter-apk/app-debug.apk" 表示成功
```
