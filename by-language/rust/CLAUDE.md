# CLAUDE.md - Rust 项目规范

## 构建与运行

- 使用 Cargo workspace 管理多 crate，根 `Cargo.toml` 定义 `[workspace]`
- 构建：`cargo build`，测试：`cargo test --workspace`，检查：`cargo clippy --workspace -- -D warnings`
- 格式化：`cargo fmt --all`，提交前必须通过 `cargo fmt --check`

## 项目结构

- 顶层 workspace 按职责拆分 crate：`core/`、`api/`、`cli/`、`shared/`
- 共享类型和 trait 放在独立 crate 中，避免循环依赖
- `examples/` 目录存放可运行示例，保持与 API 同步更新

## 错误处理

- 库 crate 使用 `thiserror` 定义结构化错误枚举，每个变体附带上下文信息
- 应用 crate 入口使用 `anyhow::Result` 简化错误传播
- 禁止在库代码中使用 `.unwrap()` 和 `.expect()`，用 `?` 操作符向上传播
- 错误消息用小写英文开头，不以句号结尾，符合 Rust 社区惯例

## Clippy 与 Lint

- 在 `lib.rs` / `main.rs` 顶部启用严格 lint：
  `#![deny(clippy::all)]` 和 `#![warn(clippy::pedantic)]`
- 允许的例外必须附带 `#[allow(...)]` 并写明原因注释
- CI 中 clippy 警告视为错误，零容忍

## 测试规范

- 单元测试写在同文件底部的 `#[cfg(test)] mod tests` 中
- 集成测试放在 `tests/` 目录，每个文件是独立测试 crate
- 使用 `#[test]` 标注，异步测试用 `#[tokio::test]`
- 测试辅助函数放在 `tests/common/mod.rs`，避免重复代码
- 属性测试使用 `proptest` crate 覆盖边界条件

## Unsafe 策略

- 默认禁止使用 `unsafe`，在 `lib.rs` 中声明 `#![forbid(unsafe_code)]`
- 确实需要时必须经过 Code Review，并附带 `// SAFETY: ...` 注释说明不变量
- unsafe 代码必须有对应的 Miri 测试（`cargo +nightly miri test`）
- FFI 边界的 unsafe 封装在独立模块中，对外暴露安全接口

## 代码风格

- 公开 API 必须有 `///` 文档注释，包含示例代码块
- 优先使用迭代器链式调用，避免手动索引循环
- 类型转换使用 `From`/`Into` trait，避免 `as` 强制转换

## 常见陷阱

- `impl` 块上的 lifetime 标注容易遗漏——当 struct 持有引用时，`impl<'a> Foo<'a>` 中的 `'a` 必须显式声明，否则编译器报错信息令人困惑
- Cargo feature 是 additive 的：同一依赖的不同 feature 会合并（feature unification），可能导致测试通过但下游用户编译失败——用 `cargo hack --feature-powerset check` 验证
- 在 async 函数中调用阻塞操作（如 `std::fs`、`std::thread::sleep`）会阻塞整个 tokio runtime 线程——必须用 `tokio::task::spawn_blocking` 或对应的 async 替代 API
