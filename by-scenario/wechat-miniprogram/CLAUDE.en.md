# WeChat Mini Program Project Conventions

## Project Structure

- `app.js` / `app.json` / `app.wxss` - Global entry point and configuration
- `pages/` - Page directory; each page is a folder containing `.wxml`, `.wxss`, `.js`, `.json` (the four-file set)
- `components/` - Custom components, same four-file structure as pages
- `utils/` - Utility functions (request wrapper, formatters, auth helpers, etc.)
- `cloud/` - Cloud functions directory (when using WeChat Cloud Development)
- `miniprogram_npm/` - npm build output — never modify manually

## WXML Template Conventions

- `wx:if` / `wx:for` must always include `wx:key` — using `*this` as key is forbidden unless it is a simple value array
- In list rendering, explicitly name `wx:for-item` and `wx:for-index` to avoid confusion in nested loops
- Prefer `bind:tap` over `bindtap` for event binding (colon syntax is clearer)
- For frequently toggled elements, use `hidden`; for infrequent toggles, use `wx:if` (reduces rendering overhead)
- Reuse templates with `<template>` + `import`; extract cross-page reusable markup into components

## WXSS Styling Conventions

- Use `rpx` as the standard unit (750rpx = screen width) — `px` is forbidden
- Global styles go in `app.wxss`; page-level styles in the corresponding `.wxss`; component styles are isolated by default
- When components need external style penetration, use `externalClasses` or `styleIsolation: 'apply-shared'`
- Avoid excessive `@import` of shared stylesheets — it impacts package size

## Page Lifecycle

- `onLoad(options)` - Receives route parameters; initiate first-screen data requests here
- `onShow` - Fires every time the page becomes visible; suitable for refreshing state (e.g., after a payment callback)
- `onReady` - DOM is ready; operations like `wx.createSelectorQuery` belong here
- `onHide` / `onUnload` - Clean up timers and cancel pending requests

## Component Lifecycle

- Declare lifecycle hooks via the `lifetimes` field: `attached` (mounted), `detached` (destroyed)
- Use `observers` instead of `watch` to monitor changes in properties and data
- Inter-component communication: parent-to-child via `properties`, child-to-parent via `triggerEvent`, cross-level via a global event bus

## wx API Usage

- Wrap all async APIs in Promises — callback hell is forbidden
- Provide a unified `wx.request` wrapper handling token refresh, error code mapping, and loading state
- Sensitive APIs (`wx.getLocation`, `wx.getUserProfile`) must check authorization status before calling
- Use `wx.setStorageSync` for lightweight data; consider cloud database for data exceeding 10MB
- Subpackage loading: keep the main package under 2MB; split subpackages by feature module

## Cloud Functions (Cloud Development)

- Each cloud function gets its own directory containing `index.js` and `package.json`
- Database operations belong in cloud functions — direct client-side collection access is forbidden for security
- Use `cloud.getWXContext()` to obtain the openid inside cloud functions — never pass it from the frontend
- Scheduled triggers are configured in `config.json` using cron expressions

## Common Commands

- Open the project directory in WeChat DevTools for live preview and debugging
- `npm run build:weapp` — Compile and build (for Taro / uni-app projects)
- `npm run preview` — Generate a preview QR code (requires WeChat DevTools CLI login)
- Upload trial version: WeChat DevTools -> Upload -> Enter version number and release notes

## Testing

- Component testing with `miniprogram-simulate`: render and interact with custom components in a Node environment
- Mock `wx.*` APIs: replace `wx.request`, `wx.getStorageSync`, and other async interfaces in tests
- Extract page logic into pure functions (e.g., data formatting, validation) for independent unit testing with Jest
- On-device debugging: WeChat DevTools -> Remote Debugging -> Scan QR code to connect

## Localization Notes

- All user-facing text is written in the target language directly — no i18n framework needed unless multi-language support is required
- Store monetary amounts in the smallest unit (e.g., cents as integers); convert for display to avoid floating-point precision issues
- Mask sensitive data in display: phone numbers (`138****8888`), ID numbers (`3101**********1234`)
- Date format: `YYYY-MM-DD`; use 24-hour time format
- Review compliance: no dynamic remote code loading; virtual payments must use WeChat Pay; user-generated content must integrate the Content Security API
