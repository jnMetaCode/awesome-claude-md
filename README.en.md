# awesome-claude-md

> **A curated collection of CLAUDE.md best practices** — Help your AI coding assistant truly understand your project

🌐 [简体中文](README.md) | **English**

[![GitHub stars](https://img.shields.io/github/stars/jnMetaCode/awesome-claude-md?style=social)](https://github.com/jnMetaCode/awesome-claude-md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://makeapullrequest.com)

---

## What is this?

`CLAUDE.md` is a project-level configuration file for Claude Code — it tells the AI "how to behave in this project."

A well-written CLAUDE.md can improve AI code quality **dramatically**. But most people don't know how to write a good one.

This project collects the best CLAUDE.md practices from GitHub, organized by language, framework, and scenario — **ready to copy and use**.

---

## Quick Start

```bash
# Find your project type and copy the matching CLAUDE.md
# Example: TypeScript + Next.js full-stack project
cp by-framework/nextjs/CLAUDE.md /your/project/CLAUDE.md

# Customize it to fit your project, then you're good to go
```

---

## Writing Guide

Before diving into templates, understand the core principles:

### What makes a good CLAUDE.md

| Principle | Explanation | Example |
|-----------|-------------|---------|
| **Specific** | Give the AI concrete rules, not vague requests | ✅ "Use pnpm, not npm" ❌ "Use a suitable package manager" |
| **Concise** | AI context windows are limited — every line must earn its place | ✅ 50 lines of focused rules ❌ 500 lines of prose |
| **Testable** | Rules should be objectively verifiable | ✅ "Functions must not exceed 50 lines" ❌ "Write clean code" |
| **Project-specific** | Generic advice is worse than no advice — write what's unique to your project | ✅ "API routes go in src/api/" ❌ "Keep code tidy" |

### Common Mistakes

```markdown
# ❌ Too vague
Please write high-quality code with good performance and readability.

# ❌ Too long — reads like a tutorial
## What is React?
React is a JavaScript library for building user interfaces... (200 more lines)

# ❌ Repeating what the AI already knows
Please use ESLint to check code quality. ESLint is a...

# ✅ Specific, concise, project-specific
- Package manager: pnpm (do not use npm/yarn)
- Testing: vitest, test files go in __tests__/
- API route naming: kebab-case, e.g. /api/user-profile
- Database: Prisma ORM, schema at prisma/schema.prisma
- Never use the `any` type
```

### Universal Template

```markdown
# CLAUDE.md

## Project Overview
[One sentence describing what this project is]

## Tech Stack
- Language: [language and version]
- Framework: [framework and version]
- Package manager: [specific tool]
- Database: [if applicable]

## Project Structure
[Key directory descriptions — only the non-obvious ones]

## Development Standards
[Specific, testable rules]

## Build & Test
[Common commands]

## Gotchas
[Pitfalls and conventions unique to this project]
```

### How to Discover What to Write

Not sure what belongs in your CLAUDE.md? Try this:

1. **Run your build**: Note every command → goes into "Build & Test"
2. **Review your last 5 code review comments**: Recurring feedback → goes into "Development Standards"
3. **Recall your last gotcha**: What trips up new team members → goes into "Gotchas"
4. **List "never do this" items**: e.g., "Never use useState in Server Components" → the most valuable content
5. **Note what AI gets wrong**: deprecated APIs, hallucinated packages → essential for preventing hallucinations

---

## Template Directory

> Each template is available in Chinese (`.md`) and English (`.en.md`).

### By Language

| Language | Template | Use Cases |
|----------|----------|-----------|
| [Python](by-language/python/) | FastAPI / Django / Data Science | Backend, AI/ML, scripting |
| [TypeScript](by-language/typescript/) | General TS project standards | Frontend & backend |
| [Go](by-language/go/) | Standard Go project layout | Backend, CLI |
| [Rust](by-language/rust/) | Cargo project standards | Systems programming, CLI |
| [Java](by-language/java/) | Spring Boot / Maven | Enterprise backend |

### By Framework

| Framework | Template | Description |
|-----------|----------|-------------|
| [Next.js](by-framework/nextjs/) | App Router + RSC | Full-stack React |
| [Vue + Nuxt](by-framework/vue-nuxt/) | Composition API + Nuxt 3 | Popular frontend stack |
| [React](by-framework/react/) | Vite + React 19 | SPA applications |
| [FastAPI](by-framework/fastapi/) | Python async backend | API services |
| [Spring Boot](by-framework/spring-boot/) | Java enterprise | Microservices |
| [Svelte](by-framework/svelte/) | SvelteKit | Lightweight full-stack |
| [uni-app](by-framework/uni-app/) | Cross-platform mini-programs | Mobile (China ecosystem) |
| [Tauri](by-framework/tauri/) | Rust + Web desktop apps | Desktop clients |

### By Scenario

| Scenario | Template | Description |
|----------|----------|-------------|
| [Monorepo](by-scenario/monorepo/) | Turborepo / pnpm workspace | Multi-package projects |
| [Microservices](by-scenario/microservices/) | Multi-service projects | Backend architecture |
| [CLI Tool](by-scenario/cli-tool/) | Command-line applications | Developer tools |
| [Chrome Extension](by-scenario/chrome-extension/) | Manifest V3 | Browser extensions |
| [WeChat Mini Program](by-scenario/wechat-miniprogram/) | Native / Taro / uni-app | Mobile (China ecosystem) |
| [Full-stack SaaS](by-scenario/fullstack-saas/) | Frontend + backend + DB + deploy | Complete products |
| [Open Source Library](by-scenario/open-source-lib/) | npm/pip packages | Publishing OSS packages |

---

## Real-world Examples

CLAUDE.md files from notable GitHub projects (continuously updated):

| Project | Language | Highlights |
|---------|----------|------------|
| [overreacted.io](https://github.com/gaearon/overreacted.io/blob/main/CLAUDE.md) | Next.js/React | Dan Abramov's blog — opinionated commit message rules, clear architecture |
| [anthropic-quickstarts](https://github.com/anthropics/anthropic-quickstarts/blob/main/CLAUDE.md) | Python/TS | Anthropic official — demonstrates multi-project CLAUDE.md structure |
| [workers-sdk](https://github.com/cloudflare/workers-sdk/blob/main/CLAUDE.md) | TypeScript | Cloudflare monorepo — strict toolchain enforcement ("never use npm/yarn") |
| [basic-memory](https://github.com/basicmachines-co/basic-memory) | Python/FastAPI | Detailed Code/Test/Verify loop, "must read full file before edits" rule |
| [react-native-testing-library](https://github.com/callstack/react-native-testing-library/blob/main/CLAUDE.md) | TypeScript | Test organization: `describe` for grouping, `test` over `it` |
| [claude-flow](https://github.com/ruvnet/claude-flow/blob/main/CLAUDE.md) | JS/TS | Leads with behavioral rules ("NEVER save to root", "NEVER commit secrets") |
| [querydsl](https://github.com/OpenFeign/querydsl/blob/main/CLAUDE.md) | Java/Maven | Multi-profile Maven builds, multi-module architecture documentation |

---

## Comparison with Other Tool Configs

| Config File | Tool | Template provided? |
|-------------|------|:------------------:|
| `CLAUDE.md` | Claude Code | ✅ |
| `.cursorrules` | Cursor | Planned |
| `AGENTS.md` | Codex CLI | Planned |
| `GEMINI.md` | Gemini CLI | Planned |
| `copilot-instructions.md` | GitHub Copilot | Planned |

---

## Contributing

We welcome your CLAUDE.md submissions! Requirements:

1. **From a real project** — no hypothetical templates
2. **Battle-tested** — proven to improve AI performance
3. **Follows the format** — consistent with existing templates

---

## Related Projects

- [agency-agents-zh](https://github.com/jnMetaCode/agency-agents-zh) — 180 AI agent experts team
- [superpowers-zh](https://github.com/jnMetaCode/superpowers-zh) — AI coding superpowers (Chinese edition)
- [ai-coding-101](https://github.com/jnMetaCode/ai-coding-101) — The complete guide to AI-assisted coding
- [ai-ui-design](https://github.com/jnMetaCode/ai-ui-design) — AI-powered UI design patterns & prompts

---

## License

MIT License

---

<div align="center">

**Write a great CLAUDE.md — let AI truly understand your project**

[Star this project](https://github.com/jnMetaCode/awesome-claude-md) · [Submit your template](https://github.com/jnMetaCode/awesome-claude-md/pulls) · [Open an Issue](https://github.com/jnMetaCode/awesome-claude-md/issues)

</div>
