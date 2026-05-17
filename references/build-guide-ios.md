# iOS 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| Debug | 开发调试构建，包含调试符号，优化级别低 | `xcodebuild -scheme MyApp -configuration Debug -destination 'platform=iOS Simulator,name=iPhone 15' build` | DerivedData |
| Release | 正式发布构建，进行优化和代码签名 | `xcodebuild -scheme MyApp -configuration Release -destination 'platform=iOS Simulator,name=iPhone 15' build` | DerivedData |
| Test | 运行单元测试 | `xcodebuild test -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 15'` | - |
| Profile | 使用Profile配置构建，用于性能分析 | `xcodebuild -scheme MyApp -configuration Profile build` | DerivedData |

## 确认编译成功

```bash
xcodebuild -showBuildSettings -scheme MyApp
# 输出最后显示 "BUILD SUCCEEDED" 即成功
```
