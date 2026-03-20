# CLAUDE.md - FastAPI 项目规范

## 构建与运行

- 依赖管理：`uv` 或 `poetry`，锁文件必须提交到仓库
- 启动开发服务器：`uvicorn app.main:app --reload --port 8000`
- 运行测试：`pytest -xvs`，Lint 检查：`ruff check . && ruff format --check .`
- Python 版本：3.11+，充分利用类型标注

## 项目结构

- `app/main.py` - 入口，注册 router 和 middleware
- `app/routers/` - 按业务域拆分路由（如 `users.py`、`orders.py`）
- `app/models/` - SQLAlchemy ORM 模型，`app/schemas/` - Pydantic v2 模型
- `app/services/` - 业务逻辑层，`app/dependencies.py` - 公共依赖注入
- `alembic/` - 数据库迁移脚本

## 异步模式

- 路由函数默认 `async def`，仅调用同步阻塞库时用 `def`
- CPU 密集型任务用 `run_in_executor` 推到线程池，不要阻塞事件循环
- 数据库用 `sqlalchemy[asyncio]` + `asyncpg`，禁止 async 中调同步阻塞 I/O
- HTTP 客户端用 `httpx.AsyncClient`，在 lifespan 中创建共享实例：
  ```python
  async def lifespan(app: FastAPI):
      app.state.http = httpx.AsyncClient()
      yield
      await app.state.http.aclose()
  ```
- 后台任务用 `BackgroundTasks` 或 Celery/ARQ，禁止在请求中做耗时操作

## Pydantic v2 规范

- 请求体和响应体必须定义 Pydantic model，禁止裸 `dict` 返回
- 请求/响应模型分开定义：`UserCreate`（入参）、`UserResponse`（出参）、`UserInDB`（内部）
- 使用 `ConfigDict(from_attributes=True)` 替代旧版 `orm_mode`，入参 model 启用 `ConfigDict(strict=True)`
- 敏感字段（password）只出现在入参 model，响应 model 用 `model_fields` 显式列出
- 字段校验用 `Field(ge=0, max_length=100)` 和 `@field_validator`
- 复用字段用 mixin 或基类，不要复制粘贴
- 枚举值用 `StrEnum`，自动序列化为字符串

## 数据库与迁移

- SQLAlchemy 2.0 风格：使用 `Mapped[]` 类型标注和 `mapped_column()`
- 数据库会话通过依赖注入：`async def get_db() -> AsyncGenerator[AsyncSession]`
- 表结构变更必须通过 Alembic：`alembic revision --autogenerate -m "描述"`
- 每个迁移文件必须有 `upgrade()` 和 `downgrade()`，downgrade 不能为空
- 迁移文件提交到 git，CI 中执行 `alembic check` 确保无遗漏

## 依赖注入

- 公共依赖（认证、分页、DB 会话）定义在 `dependencies.py`
- 使用 `Annotated[User, Depends(get_current_user)]` 简化类型声明
- yield 模式管理资源生命周期，测试中用 `app.dependency_overrides` 替换
- 嵌套依赖保持 <= 3 层，过深则重构为 service class

## Router 组织

- 每个 router 用 `APIRouter(prefix="/资源名", tags=["标签"])`，在 `main.py` 统一注册
- `main.py` 只做 `app.include_router()`，不写业务逻辑
- 路由函数只做参数校验和调用 service，业务逻辑在 `services/` 层
- RESTful 命名：`list_users`、`get_user`、`create_user`、`delete_user`

## 测试规范

- 使用 `httpx.AsyncClient` + `pytest-asyncio` 做接口测试
- 测试数据库用 SQLite 内存模式或 Testcontainers，Fixture 管理数据并按用例回滚

## 常见陷阱

- 不要在 `async def` 路由中调用同步阻塞函数（如 `requests.get`、`time.sleep`）
- `BackgroundTasks` 适合轻量任务，重任务用 Celery 或 arq
- 避免在 Depends 中做大量计算，它每次请求都会执行
- 响应 model 必须设置 `response_model`，防止意外泄露内部字段
