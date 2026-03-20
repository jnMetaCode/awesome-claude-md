# CLAUDE.md - Java Spring Boot 项目规范

## 构建与运行

- 构建工具：Maven（`mvn clean package -DskipTests`）或 Gradle（`./gradlew build`）
- 运行测试：`mvn test` 或 `./gradlew test`
- 本地启动：`mvn spring-boot:run -Dspring-boot.run.profiles=local`
- Java 版本：17+，使用 `records` 替代纯数据类

## 项目结构

- 按业务领域分包：`com.example.项目名.{domain}.{controller,service,repository,dto}`
- 避免按技术层全局分包（不要一个巨大的 `controller/` 包）
- 配置类集中在 `config/` 包，通用工具类放 `common/` 包
- `resources/` 下按 profile 管理配置：`application-{local,dev,prod}.yml`

## Spring Boot 规范

- Controller 层仅做参数校验和响应封装，业务逻辑写在 Service 层
- 使用 `@Validated` + JSR 380 注解做入参校验，禁止手动 if 判空
- 统一异常处理用 `@RestControllerAdvice`，返回标准错误响应体
- 配置属性用 `@ConfigurationProperties` 绑定，禁止散落的 `@Value`
- Bean 注入优先使用构造函数注入，禁止字段注入（`@Autowired` 在字段上）

## 数据访问层

- JPA 场景：Entity 类加 `@Entity`，Repository 继承 `JpaRepository`
- MyBatis 场景：Mapper XML 与接口同包名，SQL 保持简洁可读
- 复杂查询使用 QueryDSL 或 MyBatis 动态 SQL，禁止字符串拼接 SQL
- 数据库变更用 Flyway/Liquibase 管理，禁止手动改表结构
- 事务注解 `@Transactional` 加在 Service 方法上，标明 `rollbackFor = Exception.class`

## Lombok 策略

- 允许使用：`@Getter`、`@Builder`、`@RequiredArgsConstructor`、`@Slf4j`
- 禁止使用：`@Data`（隐式 `@Setter` 破坏不变性）、`@AllArgsConstructor`
- DTO 优先使用 Java `record`，仅在需要 Builder 模式时用 Lombok

## 测试规范

- 单元测试用 JUnit 5 + Mockito，测试类命名 `XxxTest.java`
- Service 层测试用 `@ExtendWith(MockitoExtension.class)` + `@Mock` + `@InjectMocks`
- Controller 层测试用 `@WebMvcTest` + `MockMvc`，验证状态码和响应体
- 集成测试用 `@SpringBootTest` + Testcontainers 管理外部依赖
- 测试覆盖率目标：核心业务逻辑 >= 80%，Repository 层靠集成测试覆盖

## 代码风格

- 遵循 Google Java Style Guide，日志使用 SLF4J，禁止 `System.out.println`
- 方法不超过 30 行，类不超过 300 行，超出则拆分

## 常见陷阱

- 重写 `equals()` 必须同时重写 `hashCode()`，否则 HashMap/HashSet 行为异常
- 可变对象作为 Map key 后修改其字段会导致 key 哈希值变化，entry 永远找不回来——Map key 应使用不可变对象
- `SimpleDateFormat` 不是线程安全的，多线程共享会产生诡异的日期解析错误——使用 `DateTimeFormatter`（Java 8+）替代
- `Optional.get()` 在值为空时抛出 `NoSuchElementException`——必须先 `isPresent()` 检查或使用 `orElse()` / `orElseThrow()`
