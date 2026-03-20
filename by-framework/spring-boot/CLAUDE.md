# Spring Boot 3 项目

## 技术栈
- Java 17+，Spring Boot 3.x，Spring Framework 6
- 构建工具：Maven（或 Gradle）
- 数据层：Spring Data JPA + Hibernate
- 数据库：MySQL/PostgreSQL（开发环境可用 H2）
- 测试：JUnit 5 + MockMvc + Mockito

## 项目结构（分层架构）
```
src/main/java/com/example/project/
  controller/    # REST 控制器
  service/       # 业务逻辑层
  repository/    # JPA Repository
  entity/        # JPA 实体类
  dto/           # 数据传输对象
  config/        # 配置类
  exception/     # 全局异常处理（@ControllerAdvice）
src/main/resources/
  application.yml                    # 公共配置
  application-{dev,prod}.yml         # 环境配置
```

## 开发规范
- Controller 只做参数校验和响应封装，业务逻辑放 Service 层
- Service 接口与实现分离，实现类加 `@Service` 注解
- Entity 使用 JPA 注解映射（`@Entity`, `@Table`, `@Column`）
- DTO 与 Entity 分离，使用 MapStruct 或手动转换
- 请求参数校验使用 `@Valid` + `jakarta.validation` 注解（`@NotBlank`, `@Size` 等）
- 统一响应格式：封装 `Result<T>` 类包含 code/message/data

## Spring Data JPA
- Repository 继承 `JpaRepository<Entity, IdType>`
- 方法命名查询：`findByNameAndStatus(String name, Status status)`
- 复杂查询用 `@Query` 注解写 JPQL 或原生 SQL
- 分页查询：参数加 `Pageable`，返回 `Page<Entity>`

## REST API 设计
- RESTful 风格，路径用复数名词：`/api/v1/users`、`/api/v1/users/{id}`
- `@RestController` + `@RequestMapping`，响应状态码语义正确（201/204/400/404）

## 测试
- 单元测试：`@ExtendWith(MockitoExtension.class)` + `@Mock` + `@InjectMocks`
- Controller 测试：`@WebMvcTest` + `MockMvc`（不启动完整上下文）
- 集成测试：`@SpringBootTest` + `@AutoConfigureMockMvc`
- 数据层测试：`@DataJpaTest`（自动使用内嵌数据库）

## 常用命令
```bash
./mvnw spring-boot:run              # 启动应用
./mvnw test                          # 运行所有测试
./mvnw package -DskipTests           # 打包（跳过测试）
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev  # 指定配置环境
```

## 常见陷阱
- 循环依赖：避免 Service 之间互相注入，必要时用 `@Lazy` 或重构
- N+1 查询：JPA 关联默认懒加载，列表查询用 `@EntityGraph` 或 `JOIN FETCH`
- 事务注解 `@Transactional` 只对 public 方法生效，且自调用不触发代理
- `application.yml` 中的缩进必须用空格不能用 Tab
- Spring Boot 3 从 javax 迁移到 jakarta 命名空间，注意包名变化
