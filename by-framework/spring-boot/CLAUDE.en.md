# Spring Boot 3 Project

## Tech Stack
- Java 17+, Spring Boot 3.x, Spring Framework 6
- Build tool: Maven (or Gradle)
- Data layer: Spring Data JPA + Hibernate
- Database: MySQL/PostgreSQL (H2 for development)
- Testing: JUnit 5 + MockMvc + Mockito

## Project Structure (Layered Architecture)
```
src/main/java/com/example/project/
  controller/    # REST controllers
  service/       # Business logic layer
  repository/    # JPA Repositories
  entity/        # JPA entity classes
  dto/           # Data transfer objects
  config/        # Configuration classes
  exception/     # Global exception handling (@ControllerAdvice)
src/main/resources/
  application.yml                    # Shared configuration
  application-{dev,prod}.yml         # Environment-specific configs
```

## Development Guidelines
- Controllers handle only parameter validation and response wrapping; business logic belongs in the Service layer
- Separate Service interfaces from implementations; annotate implementations with `@Service`
- Map entities with JPA annotations (`@Entity`, `@Table`, `@Column`)
- Keep DTOs and Entities separate; convert with MapStruct or manually
- Validate request parameters with `@Valid` + `jakarta.validation` annotations (`@NotBlank`, `@Size`, etc.)
- Use a unified response format: a `Result<T>` wrapper with code/message/data fields

## Spring Data JPA
- Repositories extend `JpaRepository<Entity, IdType>`
- Derived queries from method names: `findByNameAndStatus(String name, Status status)`
- Use `@Query` for complex queries with JPQL or native SQL
- Pagination: add a `Pageable` parameter, return `Page<Entity>`

## REST API Design
- Follow RESTful conventions with plural nouns in paths: `/api/v1/users`, `/api/v1/users/{id}`
- Use `@RestController` + `@RequestMapping`; return semantically correct status codes (201/204/400/404)

## Testing
- Unit tests: `@ExtendWith(MockitoExtension.class)` + `@Mock` + `@InjectMocks`
- Controller tests: `@WebMvcTest` + `MockMvc` (no full context startup)
- Integration tests: `@SpringBootTest` + `@AutoConfigureMockMvc`
- Data layer tests: `@DataJpaTest` (automatically uses an embedded database)

## Common Commands
```bash
./mvnw spring-boot:run              # Start the application
./mvnw test                          # Run all tests
./mvnw package -DskipTests           # Package (skip tests)
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev  # Start with a specific profile
```

## Common Pitfalls
- Circular dependencies: avoid mutual injection between Services; use `@Lazy` or refactor if necessary
- N+1 queries: JPA associations are lazy-loaded by default; use `@EntityGraph` or `JOIN FETCH` for list queries
- `@Transactional` only works on public methods, and self-invocation bypasses the proxy
- Indentation in `application.yml` must use spaces, never tabs
- Spring Boot 3 migrated from `javax` to the `jakarta` namespace — watch for package name changes
