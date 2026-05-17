# React 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| start / dev | 开发模式启动热重载开发服务器 | `npm start` 或 `npm run dev` | localhost:{port} |
| build | 生产环境构建，生成优化后的静态文件 | `npm run build` | `build/` 或 `out/` |
| test | 运行单元测试 | `npm test` | - |
| eject | 弹出配置（不可逆），用于深度定制 | `npm run eject` | - |

## Next.js 额外命令

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| dev | 开发服务器 | `npm run dev` | localhost:3000 |
| build | 生产构建 | `npm run build` | `.next/` |
| start | 启动生产服务器 | `npm run start` | localhost:3000 |

## 确认编译成功

```bash
npm run build
# 输出 "build" 目录表示成功
```
