# CLAUDE.md - Java Spring Boot Project Conventions

## Build & Run

- Build tool: Maven (`mvn clean package -DskipTests`) or Gradle (`./gradlew build`)
- Run tests: `mvn test` or `./gradlew test`
- Local startup: `mvn spring-boot:run -Dspring-boot.run.profiles=local`
- Java version: 17+, use `records` for pure data classes

## Project Structure

- Package by business domain: `com.example.projectname.{domain}.{controller,service,repository,dto}`
- Avoid packaging by technical layer globally (no monolithic `controller/` package)
- Configuration classes go in the `config/` package; shared utilities in `common/`
- Manage profiles under `resources/`: `application-{local,dev,prod}.yml`

## Spring Boot Conventions

- Controller layer handles only parameter validation and response wrapping — business logic belongs in the Service layer
- Use `@Validated` + JSR 380 annotations for input validation — manual null-checking with `if` is forbidden
- Unified exception handling via `@RestControllerAdvice` returning a standard error response body
- Bind configuration properties with `@ConfigurationProperties` — scattered `@Value` annotations are forbidden
- Prefer constructor injection for beans — field injection (`@Autowired` on fields) is forbidden

## Data Access Layer

- JPA: annotate entity classes with `@Entity`; repositories extend `JpaRepository`
- MyBatis: keep Mapper XML in the same package as interfaces; SQL must be concise and readable
- Complex queries use QueryDSL or MyBatis dynamic SQL — string-concatenated SQL is forbidden
- Database changes managed via Flyway/Liquibase — manual schema modifications are forbidden
- Place `@Transactional` on Service methods with `rollbackFor = Exception.class` specified

## Lombok Strategy

- Allowed: `@Getter`, `@Builder`, `@RequiredArgsConstructor`, `@Slf4j`
- Forbidden: `@Data` (implicit `@Setter` breaks immutability), `@AllArgsConstructor`
- Prefer Java `record` for DTOs; use Lombok only when the Builder pattern is needed

## Testing Conventions

- Unit tests: JUnit 5 + Mockito; test class naming: `XxxTest.java`
- Service layer tests: `@ExtendWith(MockitoExtension.class)` + `@Mock` + `@InjectMocks`
- Controller layer tests: `@WebMvcTest` + `MockMvc` — verify status codes and response bodies
- Integration tests: `@SpringBootTest` + Testcontainers for external dependencies
- Coverage target: core business logic >= 80%; Repository layer covered by integration tests

## Code Style

- Follow Google Java Style Guide; use SLF4J for logging — `System.out.println` is forbidden
- Methods should not exceed 30 lines; classes should not exceed 300 lines — split when exceeded

## Common Pitfalls

- Overriding `equals()` without overriding `hashCode()` breaks HashMap/HashSet behavior
- Using mutable objects as Map keys and then modifying their fields changes the hash — the entry becomes unretrievable; always use immutable objects as Map keys
- `SimpleDateFormat` is not thread-safe; sharing it across threads produces bizarre date parsing errors — use `DateTimeFormatter` (Java 8+) instead
- `Optional.get()` throws `NoSuchElementException` when empty — always check with `isPresent()` first or use `orElse()` / `orElseThrow()`
