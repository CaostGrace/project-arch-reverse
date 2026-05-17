# Node.js 后端项目架构模板

本文档为 Node.js 后端项目提供详细的架构文档模板。

## Node.js 特定章节

### N1. 技术栈详情

| 类别 | 技术 | 版本 | 说明 |
|------|------|------|------|
| 运行时 | Node.js | 20.x LTS | JavaScript 运行时 |
| 框架 | Express / NestJS / Fastify | - | Web 框架 |
| 语言 | TypeScript | 5.x | 类型系统 |
| ORM | Prisma / TypeORM / Sequelize | - | 数据库 ORM |
| 验证 | Zod / Joi / class-validator | - | 数据验证 |
| 认证 | Passport / jwt | - | 身份认证 |
| 日志 | Winston / Pino | - | 日志框架 |
| 缓存 | Redis / Memcached | - | 缓存 |
| 消息队列 | Bull / Kafka / RabbitMQ | - | 消息队列 |
| API 文档 | Swagger / OpenAPI | - | API 文档 |
| 测试 | Jest / Mocha | - | 测试框架 |
| 包管理 | npm / pnpm / yarn | - | 包管理器 |

### N2. 架构模式

#### MVC / 三层架构 (以 Express 为例)
```
┌─────────────────────────────────────────┐
│             Routes / Controllers        │
│  ┌─────────────────────────────────┐    │
│  │  app.get('/users', userCtrl)   │    │
│  │  处理请求路由和参数验证         │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────┐
│              Services                    │
│  ┌─────────────────────────────────┐    │
│  │  Business Logic                 │    │
│  │  userService.create(),          │    │
│  │  userService.findAll()          │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────┐
│             Repositories                 │
│  ┌─────────────────────────────────┐    │
│  │  Data Access Layer             │    │
│  │  userRepository.create(),      │    │
│  │  userRepository.findById()     │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

#### NestJS 模块化架构
```
┌─────────────────────────────────────────┐
│              Modules                     │
│  ┌──────────┐  ┌──────────┐  ┌──────┐  │
│  │   Users  │  │  Orders  │  │ Auth │  │
│  │  Module  │  │  Module  │  │Module│  │
│  └──────────┘  └──────────┘  └──────┘  │
└─────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────┐
│           Shared Modules                 │
│  ┌──────────┐  ┌──────────┐  ┌──────┐  │
│  │ Database │  │   Auth   │  │Logger│  │
│  │  Module  │  │  Guard   │  │Module│  │
│  └──────────┘  └──────────┘  └──────┘  │
└─────────────────────────────────────────┘
```

### N3. 目录结构 (Express)

```
src/
├── config/             # 配置文件
├── controllers/         # 控制器
│   └── user.controller.ts
├── services/            # 业务逻辑
│   └── user.service.ts
├── repositories/        # 数据访问
│   └── user.repository.ts
├── models/              # 数据模型
│   ├── user.model.ts
│   └── index.ts
├── routes/              # 路由定义
│   ├── index.ts
│   └── user.routes.ts
├── middlewares/         # 中间件
│   ├── auth.middleware.ts
│   └── error.middleware.ts
├── utils/               # 工具函数
├── types/               # 类型定义
├── constants/           # 常量
└── app.ts               # 应用入口
```

### N4. 目录结构 (NestJS)

```
src/
├── modules/              # 功能模块
│   ├── users/
│   │   ├── users.controller.ts
│   │   ├── users.service.ts
│   │   ├── users.module.ts
│   │   ├── dto/
│   │   └── entities/
│   ├── orders/
│   └── auth/
├── shared/              # 共享模块
│   ├── guards/
│   ├── decorators/
│   ├── filters/
│   └── interceptors/
├── config/              # 配置
├── database/            # 数据库
└── main.ts              # 入口
```

### N5. API 端点分析

#### RESTful 端点
```typescript
// users.controller.ts
@Controller('users')
export class UserController {
  @Get()           // GET /users
  async findAll(): Promise<User[]>

  @Get(':id')     // GET /users/:id
  async findOne(@Param('id') id: string): Promise<User>

  @Post()          // POST /users
  async create(@Body() createUserDto: CreateUserDto): Promise<User>

  @Patch(':id')    // PATCH /users/:id
  async update(
    @Param('id') id: string,
    @Body() updateUserDto: UpdateUserDto
  ): Promise<User>

  @Delete(':id')  // DELETE /users/:id
  async remove(@Param('id') id: string): Promise<void>
}
```

### N6. 中间件配置

```typescript
// Express 中间件链
const express = require('express')
const app = express()

app.use(helmet())                    // 安全头
app.use(cors())                       // 跨域
app.use(compression())               // 压缩
app.use(express.json())              // JSON 解析
app.use(requestLogger())             // 请求日志
app.use(authMiddleware())            // 认证
app.use('/api', routes)              // 路由
app.use(errorHandler())              // 错误处理
```

### N7. 缓存策略

| 缓存级别 | 技术 | 场景 | TTL |
|----------|------|------|-----|
| 进程缓存 | node-cache | 热点数据 | 5min |
| 分布式缓存 | Redis | 共享数据 | 30min |
| 查询缓存 | Redis | 数据库查询 | 10min |

### N8. 日志方案

```typescript
// Winston 配置
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
})
```

### N9. 监控与可观测性

```typescript
// Prometheus metrics
const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests',
  labelNames: ['method', 'route', 'status_code']
})
```

## 标准章节参考

详见 [template.md](./template.md) 中的标准章节结构。
