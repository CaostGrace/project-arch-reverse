# HarmonyOS Next 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| assembleHap | 编译 HAP（应用模块包），可独立安装运行 | `hvigorw assembleHap -p product=default -p module=entry` | `entry/build/default/outputs/hap/` |
| assembleApp | 编译 APP 包（完整应用发布包） | `hvigorw assembleApp -p product=default` | `build/outputs/app/` |
| assembleHsp | 编译 HSP（动态共享包） | `hvigorw assembleHsp -p product=default -p module=模块名` | `模块/build/outputs/hsp/` |
| build | 完整项目构建 | `hvigorw build -p product=default` | `build/` |
| clean | 清理构建产物 | `hvigorw clean` | - |

## HAP/HAR/HSP 说明

| 包类型 | 说明 | 依赖时机 |
|--------|------|----------|
| HAP | HarmonyOS Ability Package，可独立安装的应用模块 | 运行时 |
| HAR | HarmonyOS Archive，静态共享库 | 编译时 |
| HSP | HarmonyOS Shared Package，动态共享包 | 运行时 |

## 确认编译成功

```bash
hvigorw assembleHap -p product=default -p module=entry
# 输出 " assembleHap success " 表示成功
# 生成文件：entry/build/default/outputs/hap/default/xxx.hap
```
