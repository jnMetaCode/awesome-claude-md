# CLAUDE.md - Rust Project Conventions

## Build & Run

- Use Cargo workspace to manage multiple crates; define `[workspace]` in the root `Cargo.toml`
- Build: `cargo build`; Test: `cargo test --workspace`; Lint: `cargo clippy --workspace -- -D warnings`
- Format: `cargo fmt --all`; must pass `cargo fmt --check` before committing

## Project Structure

- Top-level workspace split by responsibility: `core/`, `api/`, `cli/`, `shared/`
- Shared types and traits belong in a dedicated crate to avoid circular dependencies
- `examples/` directory contains runnable examples, kept in sync with the API

## Error Handling

- Library crates: use `thiserror` to define structured error enums with context information per variant
- Application crate entry points: use `anyhow::Result` to simplify error propagation
- Never use `.unwrap()` or `.expect()` in library code — propagate errors upward with `?`
- Error messages start with lowercase, do not end with a period, following Rust community convention

## Clippy & Linting

- Enable strict lints at the top of `lib.rs` / `main.rs`:
  `#![deny(clippy::all)]` and `#![warn(clippy::pedantic)]`
- Exceptions must use `#[allow(...)]` with a comment explaining the reason
- Clippy warnings are treated as errors in CI — zero tolerance

## Testing Conventions

- Unit tests go in `#[cfg(test)] mod tests` at the bottom of the same file
- Integration tests live in the `tests/` directory; each file is an independent test crate
- Use `#[test]` for synchronous tests, `#[tokio::test]` for async tests
- Test helper functions go in `tests/common/mod.rs` to avoid code duplication
- Use the `proptest` crate for property-based testing to cover edge cases

## Unsafe Policy

- Unsafe code is forbidden by default — declare `#![forbid(unsafe_code)]` in `lib.rs`
- When genuinely needed, it must pass code review and include a `// SAFETY: ...` comment explaining invariants
- Unsafe code must have corresponding Miri tests (`cargo +nightly miri test`)
- FFI boundary unsafe code should be isolated in a dedicated module, exposing only safe interfaces externally

## Code Style

- Public APIs must have `///` doc comments including example code blocks
- Prefer iterator chains over manual index-based loops
- Use `From`/`Into` traits for type conversion — avoid `as` casts

## Common Pitfalls

- Lifetime annotations on `impl` blocks are easy to miss — when a struct holds references, `impl<'a> Foo<'a>` requires the explicit `'a`, and the compiler error messages can be confusing
- Cargo features are additive: different features of the same dependency get unified (feature unification), which can cause tests to pass locally but downstream users to fail to compile — verify with `cargo hack --feature-powerset check`
- Calling blocking operations (e.g., `std::fs`, `std::thread::sleep`) inside async functions blocks the entire tokio runtime thread — use `tokio::task::spawn_blocking` or the corresponding async alternative APIs
