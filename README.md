# awesome-claude-md

> **CLAUDE.md 最佳实践集合** — 让你的 AI 编程助手真正理解你的项目

🌐 **简体中文** | [English](README.en.md)

[![GitHub stars](https://img.shields.io/github/stars/jnMetaCode/awesome-claude-md?style=social)](https://github.com/jnMetaCode/awesome-claude-md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://makeapullrequest.com)
[![QQ群](https://img.shields.io/badge/QQ群-833585047-blue?logo=tencentqq)](https://qm.qq.com/q/x8kyqzlfDc)

---

## 这是什么？

`CLAUDE.md` 是 Claude Code 的项目级配置文件——告诉 AI "在这个项目里该怎么表现"。

一个好的 CLAUDE.md 能让 AI 的代码质量提升 **数倍**。但大多数人不知道怎么写好它。

本项目收集了 GitHub 上最好的 CLAUDE.md 实践，按语言、框架、场景分类，**复制粘贴即可用**。

---

## 快速开始

```bash
# 找到你的项目类型，复制对应的 CLAUDE.md
# 例如：TypeScript + Next.js 全栈项目
cp by-framework/nextjs/CLAUDE.md /your/project/CLAUDE.md

# 根据实际情况修改后即可使用
```

---

## 编写指南

在看模板之前，先理解核心原则：

### 好的 CLAUDE.md 的特征

| 原则 | 说明 | 示例 |
|------|------|------|
| **具体** | 告诉 AI 具体的规则，而不是模糊的要求 | ✅ "使用 pnpm，不要用 npm" ❌ "用合适的包管理器" |
| **简短** | AI 上下文窗口有限，每一行都要有价值 | ✅ 50 行精炼规则 ❌ 500 行长篇大论 |
| **可测试** | 规则应该能被客观判断是否遵守 | ✅ "函数不超过 50 行" ❌ "写简洁的代码" |
| **项目特定** | 写通用文档不如不写，要写这个项目独有的约定 | ✅ "API 路由放在 src/api/" ❌ "保持代码整洁" |

### 常见错误

```markdown
# ❌ 太模糊
请写高质量的代码，注意性能和可读性。

# ❌ 太长，像教程
## 什么是 React？
React 是一个用于构建用户界面的 JavaScript 库...（省略 200 行）

# ❌ 重复 AI 已知的知识
请使用 ESLint 来检查代码质量。ESLint 是一个...

# ✅ 具体、简短、项目特定
- 包管理器：pnpm（不要用 npm/yarn）
- 测试：vitest，测试文件放在 __tests__/ 下
- API 路由命名：kebab-case，如 /api/user-profile
- 数据库：Prisma ORM，schema 在 prisma/schema.prisma
- 绝不使用 any 类型
```

### 万能模板

```markdown
# CLAUDE.md

## 项目概述
[一句话描述这个项目是什么]

## 技术栈
- 语言：[语言和版本]
- 框架：[框架和版本]
- 包管理器：[具体工具]
- 数据库：[如有]

## 项目结构
[关键目录说明，只写不明显的]

## 开发规范
[具体的、可测试的规则]

## 构建和测试
[常用命令]

## 注意事项
[这个项目独有的坑和约定]
```

### 如何发现该写什么

不知道 CLAUDE.md 里该写什么？试试这个方法：

1. **跑一遍构建**：记下每一条命令 → 写入"构建和测试"
2. **看最近 5 次 Code Review 意见**：重复出现的问题 → 写入"开发规范"
3. **回忆上次踩的坑**：新人最容易犯的错 → 写入"注意事项"
4. **列出"绝不要做的事"**：比如"绝不在 Server Component 中使用 useState" → 这是最有价值的内容
5. **写下 AI 最容易搞错的东西**：比如用了已弃用的 API、引入了不存在的包 → 防幻觉必备

---

## 模板目录

> 每个模板均提供中文版（`.md`）和英文版（`.en.md`）。

### 按语言

| 语言 | 模板 | 适用场景 |
|------|------|---------|
| [Python](by-language/python/) | FastAPI / Django / 数据科学 | 后端、AI/ML、脚本 |
| [TypeScript](by-language/typescript/) | 通用 TS 项目规范 | 前后端通用 |
| [Go](by-language/go/) | 标准 Go 项目布局 | 后端、CLI |
| [Rust](by-language/rust/) | Cargo 项目规范 | 系统级、CLI |
| [Java](by-language/java/) | Spring Boot / Maven | 企业级后端 |

### 按框架

| 框架 | 模板 | 说明 |
|------|------|------|
| [Next.js](by-framework/nextjs/) | App Router + RSC | 全栈 React |
| [Vue + Nuxt](by-framework/vue-nuxt/) | Composition API + Nuxt 3 | 国内主流前端 |
| [React](by-framework/react/) | Vite + React 19 | SPA 应用 |
| [FastAPI](by-framework/fastapi/) | Python 异步后端 | API 服务 |
| [Spring Boot](by-framework/spring-boot/) | Java 企业级 | 微服务 |
| [Svelte](by-framework/svelte/) | SvelteKit | 轻量全栈 |
| [uni-app](by-framework/uni-app/) | 跨端小程序 | 国内移动端 |
| [Tauri](by-framework/tauri/) | Rust + Web 桌面应用 | 桌面客户端 |

### 按场景

| 场景 | 模板 | 说明 |
|------|------|------|
| [Monorepo](by-scenario/monorepo/) | Turborepo / pnpm workspace | 多包项目 |
| [微服务](by-scenario/microservices/) | 多服务项目 | 后端架构 |
| [CLI 工具](by-scenario/cli-tool/) | 命令行应用 | 开发者工具 |
| [Chrome 插件](by-scenario/chrome-extension/) | Manifest V3 | 浏览器扩展 |
| [微信小程序](by-scenario/wechat-miniprogram/) | 原生 / Taro / uni-app | 国内移动端 |
| [全栈 SaaS](by-scenario/fullstack-saas/) | 前后端 + 数据库 + 部署 | 完整产品 |
| [开源库](by-scenario/open-source-lib/) | npm/pip 包 | 发布开源包 |

---

## 真实案例

来自 GitHub 上知名项目的 CLAUDE.md（持续收集中）：

| 项目 | 语言 | 亮点 |
|------|------|------|
| [overreacted.io](https://github.com/gaearon/overreacted.io/blob/main/CLAUDE.md) | Next.js/React | Dan Abramov 的博客，commit message 规范有个性，架构描述清晰 |
| [anthropic-quickstarts](https://github.com/anthropics/anthropic-quickstarts/blob/main/CLAUDE.md) | Python/TS | Anthropic 官方，展示多子项目如何共用一个 CLAUDE.md |
| [workers-sdk](https://github.com/cloudflare/workers-sdk/blob/main/CLAUDE.md) | TypeScript | Cloudflare monorepo，严格的工具链约束（"绝不用 npm/yarn"） |
| [basic-memory](https://github.com/basicmachines-co/basic-memory) | Python/FastAPI | 详细的 Code/Test/Verify 循环，"编辑前必须完整读取文件" |
| [react-native-testing-library](https://github.com/callstack/react-native-testing-library/blob/main/CLAUDE.md) | TypeScript | 测试组织规范：用 `describe` 分组，`test` 优于 `it` |
| [claude-flow](https://github.com/ruvnet/claude-flow/blob/main/CLAUDE.md) | JS/TS | 以行为规则开头（"永不保存到根目录"、"永不提交密钥"） |
| [querydsl](https://github.com/OpenFeign/querydsl/blob/main/CLAUDE.md) | Java/Maven | Maven 多 profile 构建文档，多模块架构 |

---

## 对比其他工具配置

| 配置文件 | 工具 | 本项目提供模板？ |
|---------|------|:---:|
| `CLAUDE.md` | Claude Code | ✅ |
| `.cursorrules` | Cursor | 计划中 |
| `AGENTS.md` | Codex CLI | 计划中 |
| `GEMINI.md` | Gemini CLI | 计划中 |
| `copilot-instructions.md` | GitHub Copilot | 计划中 |

---

## 贡献

欢迎提交你的 CLAUDE.md！要求：

1. **来自真实项目** — 不接受假想的模板
2. **经过验证** — 确实让 AI 表现更好
3. **符合格式** — 参考已有模板的格式

---

## 社区交流

| 群名 | 群号 | 加入方式 |
|------|------|---------|
| AI Agent 中文实践群 | **833585047** | [点击加入](https://qm.qq.com/q/x8kyqzlfDc) |

---

## 相关项目

- [agency-agents-zh](https://github.com/jnMetaCode/agency-agents-zh) — 180 个 AI 智能体专家团队
- [superpowers-zh](https://github.com/jnMetaCode/superpowers-zh) — AI 编程超能力中文版

---

## 许可证

MIT License

---

<div align="center">

**写好 CLAUDE.md，让 AI 真正理解你的项目**

[Star 本项目](https://github.com/jnMetaCode/awesome-claude-md) · [提交你的模板](https://github.com/jnMetaCode/awesome-claude-md/pulls) · [提交 Issue](https://github.com/jnMetaCode/awesome-claude-md/issues)

</div>
