# Project Context

## Purpose
nest-admin 是一个基于 NestJS 的后台管理系统，提供完整的权限管理、用户管理、系统配置等功能。作为劳动教育实践平台的后端服务，支持前后端分离架构。

## Tech Stack

### 核心框架
- **NestJS 11** - 后端框架
- **TypeScript 5.8** - 编程语言
- **Fastify** - HTTP 服务器（替代 Express，性能更优）
- **TypeORM 0.3** - ORM 框架

### 数据存储
- **MySQL 8.x** - 主数据库
- **Redis** - 缓存、会话管理、消息队列

### 认证与安全
- **Passport.js** - 认证框架
- **JWT** - Token 认证
- **RBAC** - 基于角色的权限控制
- **Helmet** - 安全中间件
- **Throttler** - 请求限流

### 其他依赖
- **Socket.IO** - WebSocket 实时通信
- **Bull** - 任务队列
- **Winston** - 日志系统
- **Swagger** - API 文档
- **class-validator / class-transformer** - DTO 验证与转换
- **nestjs-cls** - 请求上下文（CLS）
- **七牛云 (qiniu)** - 文件存储

### 开发工具
- **pnpm 9** - 包管理器
- **ESLint (@antfu/eslint-config)** - 代码检查
- **Jest** - 单元测试
- **simple-git-hooks + lint-staged** - Git 钩子

## Project Conventions

### Code Style
- 使用 **2 空格缩进**
- 使用 **单引号**
- 遵循 `@antfu/eslint-config` 规范
- 路径别名：`~/` 指向 `src/` 目录
- 文件命名：使用 **kebab-case**（如 `user.service.ts`）
- 类命名：使用 **PascalCase**（如 `UserService`）

### Architecture Patterns

#### 目录结构
```
src/
├── common/          # 通用模块（装饰器、过滤器、拦截器、管道、DTO）
├── config/          # 配置文件
├── constants/       # 常量定义
├── global/          # 全局工具（环境变量）
├── helper/          # 辅助函数
├── migrations/      # 数据库迁移
├── modules/         # 业务模块
│   ├── auth/        # 认证模块
│   ├── system/      # 系统管理（用户、角色、菜单、部门等）
│   ├── user/        # 用户模块
│   ├── tasks/       # 定时任务
│   ├── tools/       # 工具模块（文件存储等）
│   └── ...
├── shared/          # 共享服务（数据库、Redis、日志、邮件）
├── socket/          # WebSocket 模块
└── utils/           # 工具函数
```

#### 模块结构规范
每个业务模块通常包含：
- `*.module.ts` - 模块定义
- `*.controller.ts` - 控制器
- `*.service.ts` - 服务层
- `*.entity.ts` - 数据库实体
- `dto/*.dto.ts` - 数据传输对象

#### 实体基类
- `CommonEntity` - 基础实体（id, createdAt, updatedAt）
- `CompleteEntity` - 完整实体（继承 CommonEntity，增加 createBy, updateBy, creator, updater）

#### 全局配置
- 全局异常过滤器：`AllExceptionsFilter`
- 全局拦截器：`ClassSerializerInterceptor`, `TransformInterceptor`, `TimeoutInterceptor`, `IdempotenceInterceptor`
- 全局守卫：`JwtAuthGuard`, `RbacGuard`, `ThrottlerGuard`
- 全局管道：`ValidationPipe`（whitelist 模式，自动转换）

### Testing Strategy
- 使用 **Jest** 进行单元测试
- 测试文件命名：`*.spec.ts`
- 测试目录与源码同级
- 运行测试：`pnpm test`
- 监听模式：`pnpm test:watch`

### Git Workflow
- 主分支：`main`
- 使用 **simple-git-hooks** 在 pre-commit 时执行 lint-staged
- 提交前自动运行 ESLint 修复
- 提交信息规范：使用语义化提交（feat, fix, chore, docs 等）

## Domain Context

### 系统模块说明
- **auth** - 登录、登出、Token 管理、OAuth（Google）
- **system** - 系统管理
  - dept - 部门管理
  - role - 角色管理
  - menu - 菜单管理
  - user - 用户管理（在 modules/user）
  - dict-type / dict-item - 字典管理
  - param-config - 参数配置
  - log - 日志管理（登录日志、任务日志、验证码日志）
  - task - 定时任务管理
  - online - 在线用户
  - serve - 服务监控
- **tools** - 工具模块（文件存储）
- **netdisk** - 网盘功能
- **todo** - 待办事项
- **tasks** - 定时任务调度
- **sse** - Server-Sent Events
- **health** - 健康检查

### 默认账号
- 超级管理员：`admin` / `a123456`
- 新用户默认密码：`a123456`

## Important Constraints

### 环境要求
- Node.js >= 20.0.0
- pnpm >= 9
- MySQL 8.x+
- Docker 20.x+（docker compose 2.17.0+）

### 配置文件
- `.env` - 公共配置
- `.env.development` - 开发环境
- `.env.production` - 生产环境
- `.env.local` - 本地覆盖（优先级最高）

### 安全约束
- 所有 API 默认需要 JWT 认证（除白名单）
- 启用 RBAC 权限控制
- 启用请求限流
- 启用 CORS

## External Dependencies

### 必需服务
- **MySQL** - 主数据库（端口通过 DB_PORT 配置）
- **Redis** - 缓存与会话（端口通过 REDIS_PORT 配置）

### 可选服务
- **七牛云** - 文件存储服务
- **SMTP 邮件服务** - 邮件发送

### API 文档
- Swagger UI：`http://localhost:7001/api-docs/`

### Docker 部署
```bash
# 快速启动所有服务
pnpm docker:up

# 查看日志
pnpm docker:logs

# 停止服务
pnpm docker:down
```

### 数据库迁移
```bash
# 运行迁移
pnpm migration:run

# 生成迁移
pnpm migration:generate

# 回滚迁移
pnpm migration:revert
```
