# React 19 + Vite SPA 项目

## 技术栈
- React 19 + TypeScript，Vite 6 构建
- React Router v7 路由管理
- Zustand 状态管理（轻量场景用 Jotai）
- Tailwind CSS 4 样式方案
- Vitest + React Testing Library 测试

## 项目结构
```
src/
  components/    # 可复用 UI 组件（纯展示，无业务逻辑）
  pages/         # 页面级组件，对应路由
  hooks/         # 自定义 hooks
  stores/        # Zustand store 定义
  lib/           # 工具函数、API 客户端
  types/         # TypeScript 类型定义
```

## 开发规范
- 组件使用函数式写法 + TypeScript，Props 用 interface 定义
- 优先使用 React 19 新特性：`use()` hook 处理 Promise/Context，`useOptimistic` 管理乐观更新
- 了解 Server Components 概念，但本项目为纯 SPA，所有组件均为 Client Components
- 使用 `React.lazy()` + `Suspense` 做路由级代码分割
- 状态管理原则：组件本地状态用 `useState`，跨组件共享用 Zustand，服务端状态用 TanStack Query

## 路由
- React Router v7 使用 `createBrowserRouter` + `RouterProvider` 模式
- 数据加载用 `loader`，表单提交用 `action`
- 路由定义集中在 `src/router.tsx`

## 样式
- Tailwind CSS utility class 优先，组件变体用 `clsx` 或 `cva` 管理条件样式

## 测试
- 运行测试：`npx vitest` 或 `npx vitest run`
- 单个文件：`npx vitest src/components/Button.test.tsx`
- 组件测试用 `render` + `screen` + `userEvent`，优先按角色/文本查询 DOM
- 异步断言使用 `waitFor` 或 `findBy*` 查询

## 常用命令
```bash
npm run dev          # 启动开发服务器（默认 5173 端口）
npm run build        # 生产构建，输出到 dist/
npm run preview      # 预览生产构建
npm run lint         # ESLint 检查
npm run test         # 运行测试
```

## 常见陷阱
- Tailwind v4 使用 CSS-first 配置，不再需要 `tailwind.config.js`
- 路由中使用 `loader` 返回数据时，组件通过 `useLoaderData()` 获取
