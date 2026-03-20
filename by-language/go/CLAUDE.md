# CLAUDE.md - Go 项目规范

## 构建与运行

- 使用 Go Modules 管理依赖，执行 `go mod tidy` 保持 go.sum 干净
- 构建命令：`go build ./cmd/...`，测试命令：`go test ./... -race -count=1`
- Lint 检查：`golangci-lint run`，确保零警告

## 项目结构

- `cmd/` - 各可执行文件入口，每个子目录包含一个 `main.go`
- `internal/` - 私有业务逻辑，禁止被外部项目引用
- `pkg/` - 可被外部引用的公共库代码
- `api/` - Protobuf/OpenAPI 定义文件
- `configs/` - 配置文件模板（不提交实际密钥）

## 错误处理

- 禁止在库代码中使用 `panic`，仅在 `main` 初始化阶段允许 `log.Fatal`
- 使用 `fmt.Errorf("操作描述: %w", err)` 包装错误，保留错误链
- 自定义错误类型实现 `Error()` 接口，必要时实现 `Unwrap()`
- 在调用方统一处理错误，避免重复日志记录

## 接口设计

- 接口定义在消费者侧，而非实现侧（Accept interfaces, return structs）
- 接口保持小而聚焦，优先单方法接口（如 `io.Reader`）
- 使用接口做依赖注入，便于 mock 测试

## 测试规范

- 单元测试文件与源码同目录，命名 `xxx_test.go`
- 使用 `testify/assert` 和 `testify/require` 简化断言
- Table-driven tests 处理多场景，子测试用 `t.Run("场景名", ...)`
- 集成测试加 `//go:build integration` 标签，CI 中单独运行
- Mock 使用 `testify/mock` 或 `gomock`，接口定义处生成

## 代码风格

- 严格遵循 `gofmt` 格式化，提交前执行 `goimports`
- 导出函数和类型必须有 GoDoc 注释，以函数名开头
- Context 作为函数第一个参数传递，命名为 `ctx`
- 不使用 `init()` 函数，显式初始化优于隐式
- Channel 和 Goroutine 必须有明确的退出机制，防止泄漏

## 常见陷阱

- 向 nil map 写入会 panic，使用前必须 `make(map[K]V)` 初始化
- 无缓冲 channel 在 goroutine 中发送但无接收方会导致 goroutine 永久泄漏
- 短变量声明 `:=` 在内层作用域会遮蔽外层 `err`，导致错误被静默丢弃——用 `var err error` 提前声明或检查 `golangci-lint` 的 `shadow` 规则
- `time.After` 在循环中使用会每次迭代创建新 timer 且旧 timer 不被 GC，导致内存泄漏——改用 `time.NewTimer` + `Reset()`
