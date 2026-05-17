# Vue.js 编译指南

## 编译目标

| 编译目标 | 用途说明 | 编译命令 | 输出位置 |
|----------|----------|----------|----------|
| dev / serve | 开发模式启动热重载开发服务器 | `npm run dev` 或 `npm run serve` | localhost:{port} |
| build | 生产环境构建，生成优化后的静态文件 | `npm run build` | `dist/` |
| preview | 本地预览生产构建结果 | `npm run preview` | localhost:{port} |
| lint | 运行 ESLint 检查代码规范 | `npm run lint` | - |
| test:unit | 运行单元测试 | `npm run test:unit` | - |

## 确认编译成功

```bash
npm run build
# 输出 "dist" 目录表示成功
```
