# CLAUDE.md - Next.js App Router 项目规范

## 项目结构

- 使用 Next.js App Router（`app/` 目录），不使用 Pages Router
- 目录约定：
  - `app/` - 路由与页面
  - `components/` - 可复用组件，按功能分子目录（`components/ui/`、`components/layout/`）
  - `lib/` - 工具函数、API 客户端、数据库操作
  - `types/` - 全局类型定义
  - `public/` - 静态资源

## Server / Client 组件

- 只在需要交互、浏览器 API、hooks 时才加 `'use client'`
- Server Component 中直接 `await` 异步数据，不需要 `useEffect` + `useState`
- 将交互逻辑拆分为小的 Client Component，嵌入 Server Component 中，最小化客户端 bundle

## 数据获取

- Server Component 中直接调用数据库或内部 API，不经过 HTTP
- 使用 `fetch()` 时利用 Next.js 内置缓存：`fetch(url, { next: { revalidate: 3600 } })`
- 需要实时数据时用 `{ cache: 'no-store' }` 或在 layout/page 中 `export const dynamic = 'force-dynamic'`
- 变更操作使用 Server Actions（`'use server'` 函数），配合 `revalidatePath()` / `revalidateTag()` 刷新缓存
- 表单提交优先用 `<form action={serverAction}>`，客户端补充用 `useFormStatus` 和 `useActionState`

## API Routes

- API 路由放在 `app/api/` 下，导出 `GET`、`POST` 等命名函数
- 使用 `NextRequest` / `NextResponse` 类型
- 返回数据统一格式：`{ data, error, message }`
- 鉴权中间件写在 `middleware.ts`（项目根目录），匹配 `config.matcher`

## 样式

- 使用 Tailwind CSS，不写自定义 CSS 文件除非绝对必要
- 组件样式用 Tailwind utility class 直接写在 JSX 中
- 复杂样式组合用 `clsx` 或 `cn`（`lib/utils.ts` 中封装 `twMerge + clsx`）
- 响应式断点顺序：`sm:` > `md:` > `lg:` > `xl:`，移动端优先

## TypeScript 规范

- 开启 strict 模式，禁止 `any`
- 页面 props 类型用 Next.js 内置泛型：`type Props = { params: Promise<{ slug: string }>; searchParams: Promise<Record<string, string>> }`
- Server Actions 参数使用 Zod 校验，不信任客户端输入

## 常用命令

- `pnpm dev` - 启动开发服务 (http://localhost:3000)
- `pnpm build` - 构建生产产物
- `pnpm lint` - 运行 `next lint`
- `pnpm typecheck` - 运行 `tsc --noEmit`
- `pnpm test` - 运行 Vitest 测试

## 常见陷阱

- Server Component 向 Client Component 传递 props 时，值必须可序列化——不能传函数、class 实例或 Date 对象
- Parallel Routes（`@folder`）与 Intercepting Routes 同时使用时，slot 匹配规则容易冲突——确保每个 parallel slot 都有对应的 `default.tsx`
- Middleware 在 Edge Runtime 上运行，冷启动延迟明显——避免在 middleware 中做重逻辑或大量 I/O
- `revalidatePath` / `revalidateTag` 只在 Server Action 或 Route Handler 中生效，在普通 Server Component 中调用无效
