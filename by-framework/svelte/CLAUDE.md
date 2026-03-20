# SvelteKit 项目

## 技术栈
- Svelte 5 + SvelteKit 2，TypeScript 严格模式
- Svelte 5 Runes 响应式系统
- Vitest 单元测试 + Playwright E2E 测试

## 项目结构
```
src/
  routes/       # 文件系统路由，+page.svelte / +layout.svelte / +server.ts
  lib/          # 共享代码，可通过 $lib 别名引用
    components/ # 可复用组件
    server/     # 仅服务端代码（数据库、密钥等）
  params/       # 路由参数匹配器
static/         # 静态资源
```

## Svelte 5 Runes 语法
- `$state(value)` 声明响应式状态，替代旧的 `let` 响应式变量
- `$derived(expr)` 派生计算值，替代 `$:` 语法
- `$effect(() => {})` 副作用，替代 `$:` 语句块
- `$props()` 声明组件属性，替代 `export let`
- `$bindable()` 声明可双向绑定的 prop
- 禁止使用旧版 Svelte 4 语法（`$:`, `export let` 等）

## SvelteKit 路由与数据加载
- `+page.ts` 中的 `load` 函数在客户端和服务端均可执行
- `+page.server.ts` 中的 `load` 仅在服务端执行，可安全访问数据库
- 表单处理使用 `+page.server.ts` 中的 `actions`，配合 `use:enhance` 渐进增强
- API 路由放在 `+server.ts` 中，导出 `GET/POST/PUT/DELETE` 函数

## 开发规范
- TypeScript 严格模式：`tsconfig.json` 中启用 `strict: true`
- 组件文件使用 `<script lang="ts">` 标签
- 服务端专用代码放在 `$lib/server/` 下，SvelteKit 会阻止客户端导入
- 环境变量：公开的用 `$env/static/public`，私密的用 `$env/static/private`

## 测试
- 单元测试：`npx vitest run`
- E2E 测试：`npx playwright test`
- 组件测试使用 `@testing-library/svelte` 的 `render` + `screen`
- E2E 测试文件放在 `tests/` 目录，使用 `test.describe` 组织用例

## 常用命令
```bash
npm run dev          # 启动开发服务器
npm run build        # 构建生产版本
npm run preview      # 预览构建产物
npm run test:unit    # 运行 Vitest 单元测试
npm run test:e2e     # 运行 Playwright E2E 测试
npm run check        # svelte-check 类型检查
```

## 常见陷阱
- `$state` 对象是深度响应式的，但重新赋值整个对象才会触发引用变更
- `load` 函数的返回值会序列化传给客户端，不能包含不可序列化的值（如函数、类实例）
- 使用 `{@html}` 渲染 HTML 时必须确保内容已消毒，防止 XSS
- `goto()` 导航默认会重新运行所有活跃的 `load` 函数；传 `invalidateAll: false` 可阻止重新运行其他页面的 `load`，但目标页面自身的 `load` 仍然会执行
