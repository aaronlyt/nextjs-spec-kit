# nextjs-spec-kit

see https://github.com/github/spec-kit

**new nextjs commands:**
- /nextjs-plan.md 
- /nextjs-implement.md
- /nextjs-tasks.md

**new nextjs templates:**
- nextjs-plan-template.md
- nextjs-task-template.md


          
**发布概览**
- Next.js Spec Kit 小升级：用 `nextjs-plan.md`、`nextjs-tasks.md`、`nextjs-implement.md` 打通“设计 → 任务 → 落地”闭环。
- 目标：让中大型 Next.js 项目实现可追踪、可验证、可协作的全流程工程化。

**核心新增功能**
- 架构决策引擎
  - 路由模式选择：App Router / Pages Router（读取用户偏好）
  - 渲染策略矩阵：`SSG`（公开+SEO）、`SSR`（用户态）、`CSR`（高交互）、`SSR Streaming`（实时）
  - 数据库决策：默认 `PostgreSQL + Prisma`，关系复杂/灵活时自动切换
  - 样式默认：`Tailwind CSS`（支持自定义）
  - 状态管理五层策略：Auth、全局状态、服务端状态、组件状态、表单状态（基于 `state_manage.md`）
  - API 模式：表单走 `Server Actions`，对外接口用 `Route Handlers`
  - 部署默认：`Vercel`
- 任务生成器（按阶段分组）
  - Phase 1 Setup：版本/路由/依赖/样式/环境变量（来自 Section 0 + 8）
  - Phase 2 基础设施：数据库/Prisma/验证架构/中间件（来自 Section 5 + 4，阻塞后续）
  - Phase 3 用户故事：为每个 US 生成组件（C#）、API（A#）、实体（ENT#）、测试（AC 对应）
  - Final Phase 优化：性能/安全/可用性/监控/部署（来自 Section 6）
  - 并行规则：[P] 仅在不同文件可并行，同文件强制串行
- 全链路 Traceability
  - Plan Section → Task Phase 映射
  - User Story → 任务分组（DB/API/UI/Pages/Tests）映射
  - Requirement/FR/ENT → 实现任务与测试任务双向追踪
- 测试驱动与校验闭环
  - 先测后码：AC 用 Given-When-Then 生成测试规格与命名约定
  - 阶段检查点：每个用户故事独立可验证
  - 完成校验：`npm run build`、`npx tsc --noEmit`、`npm test`、数据库迁移、环境变量
  - 进度标记：任务在 `tasks.md` 用 `[X]` 实时标记，生成汇总报告
- Next.js 规范落地
  - App Router 结构：`app/`、`app/api/`、`[route]/`、`layout.tsx`
  - Pages Router 结构：`pages/`、`pages/api/`
  - API 统一结构：`POST/GET/PUT/DELETE` 路由清晰、校验前后端一致（`Zod`）
  - 组件命名规范：`{Entity}Form`、`{Entity}List`、`{Entity}Viewer`
- 错误上下文与恢复建议
  - 任务失败时输出上下文（任务/文件/阶段/错误详情）与修复选项
- 脚本化入口与预检查
  - 预检查：运行 `.specify/scripts/bash/check-prerequisites.sh --json` 抽取 `FEATURE_DIR`、`AVAILABLE_DOCS`
  - 生成计划：`.specify/scripts/bash/setup-plan.sh --json`（支持复杂字符串转义）
