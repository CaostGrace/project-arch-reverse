# Node.js 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| dev / start:dev | 开发模式启动热重载服务器 | `npm run dev` 或 `npm run start:dev` | localhost:{port} |
| build | TypeScript 编译或打包构建 | `npm run build` | `dist/` 或 `build/` |
| start | 生产环境启动 | `npm start` 或 `npm run start:prod` | - |
| test | 运行单元测试 | `npm test` | - |
| lint | 运行代码规范检查 | `npm run lint` | - |

## NestJS 额外命令

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| start:dev | 开发热重载 | `nest start --watch` | localhost:3000 |
| build | 生产构建 | `nest build` | `dist/` |
| start:prod | 生产启动 | `node dist/main` | - |

## 确认编译成功

```bash
npm run build
# 无报错表示成功
```
