# 微信小程序项目规范

## 项目结构

- `app.js` / `app.json` / `app.wxss` - 全局入口与配置
- `pages/` - 每个页面含 `.wxml` `.wxss` `.js` `.json` 四件套
- `components/` - 自定义组件（同四件套）；`utils/` - 工具函数
- `cloud/` - 云函数；`miniprogram_npm/` - npm 产物，禁止手动修改

## WXML 模板规范

- 使用 `wx:if` / `wx:for` 时必须添加 `wx:key`，禁止使用 `*this` 作为 key（除非是简单值数组）
- 列表渲染中 `wx:for-item` 和 `wx:for-index` 显式命名，避免嵌套时混淆
- 事件绑定优先使用 `bind:tap` 而非 `bindtap`（冒号语法更清晰）
- 条件渲染频繁切换用 `hidden`，不频繁切换用 `wx:if`（减少渲染开销）
- 模板复用使用 `<template>` + `import`，跨页面复用提取为 component

## WXSS 样式规范

- 尺寸单位统一使用 `rpx`（750rpx = 屏幕宽度），禁止使用 `px`
- 全局样式写 `app.wxss`，页面级样式写对应 `.wxss`，组件样式默认隔离
- 组件需要外部样式穿透时使用 `externalClasses` 或 `styleIsolation: 'apply-shared'`
- 禁止使用 `@import` 引入过多公共样式，影响包体积

## 生命周期

- Page：`onLoad` 接收参数发请求，`onShow` 刷新状态，`onReady` 操作 DOM，`onHide`/`onUnload` 清理
- Component：`lifetimes` 声明 `attached`/`detached`，`observers` 监听数据变化
- 组件通信：父传子 `properties`，子传父 `triggerEvent`，跨层级用事件总线

## wx API 使用规范

- 所有异步 API 封装为 Promise，禁止回调地狱
- `wx.request` 统一封装，处理 token 刷新、错误码映射、loading 状态
- 敏感 API（`wx.getLocation`、`wx.getUserProfile`）必须先检查授权状态
- 本地存储 `wx.setStorageSync` 用于轻量数据，超过 10MB 考虑云数据库
- 分包加载：主包控制在 2MB 以内，按功能模块拆分子包

## 云函数规范（云开发）

- 每个云函数一个目录含 `index.js` 和 `package.json`
- 数据库操作放云函数端，禁止小程序端直接操作集合
- 用 `cloud.getWXContext()` 获取 openid，禁止前端传入

## 常用命令

- 在微信开发者工具中打开项目目录，实时预览和调试
- `npm run build:weapp` — 编译构建（Taro / uni-app 项目）
- `npm run preview` — 生成预览二维码（需登录微信开发者工具 CLI）
- 上传体验版：微信开发者工具 → 上传 → 填写版本号和备注

## 测试

- 组件测试使用 `miniprogram-simulate`：在 Node 环境中渲染和操作自定义组件
- Mock `wx.*` API：在测试中替换 `wx.request`、`wx.getStorageSync` 等异步接口
- 页面逻辑提取为纯函数（如数据格式化、校验），独立用 Jest 做单元测试
- 真机调试：微信开发者工具 → 真机调试 → 扫码连接

## 中文开发注意事项

- 金额使用分为单位（整数），展示时转换为元，避免浮点精度问题
- 手机号、身份证号脱敏：`138****8888`、`3101**********1234`
- 日期格式 `YYYY年MM月DD日` 或 `YYYY-MM-DD`，24 小时制
- 审核：禁止动态加载远程代码、虚拟支付走微信支付、UGC 接入内容安全 API
