# Copilot / AI Agent Instructions — spring-boot-project-example

Purpose: Short, actionable guidance to get productive in this repository.

- **Big picture**
  - Java 17 Spring Boot service exposing REST APIs for `Customer` resources; data persisted in MongoDB.
  - Typical flow: controller -> service impl (`impl/`) -> repository (`repository/`) -> MongoDB.
  - MapStruct mappers in `mapper/` perform DTO ↔ entity conversions and use a strict unmapped policy.
  - Controller methods annotated with `@ApiLogger` are captured by AOP and written to `ApiLogEntity` via `ApiLogRepository`.

- **Build / run / tests (exact commands)**
  - Build (with annotation processing): `mvn -U clean package`.
  - Run locally: `mvn spring-boot:run` or run the `SpringBootProjectExampleApplication` main class.
  - Tests: `mvn test` (note: no embedded Mongo configured by default; tests typically mock DB interactions).

- **Local environment**
  - Java 17 and Maven required. MongoDB defaults: `localhost:27017`, database `spring-boot-project-example` (see `src/main/resources/application.properties`).
  - Enable Lombok and annotation processing in your IDE so MapStruct + Lombok compile correctly.

- **Repo-specific patterns & gotchas**
  - MapStruct: `@MapperConfig(unmappedTargetPolicy = ReportingPolicy.ERROR)` — altering DTO/entity fields requires updating mappers or adding explicit ignores.
  - DTOs live in `dto/`, entities in `entity/`. Many mappers intentionally ignore `id` on create (see `CustomerMapper`).
  - API logging: `@ApiLogger` on controllers; `ApiLogAspect` depends on `CommonUtility.getHttpServletRequest()` and a `customObjectMapper` bean provided in `spring/config/CommonConfig.java`.
  - Repositories extend `MongoRepository<Entity, String>` and are auto-scanned (`@EnableMongoRepositories`).

- **Integration points**
  - MongoDB: runtime dependency. For tests that touch API logging, provide or mock an `HttpServletRequest` or stub `CommonUtility.getHttpServletRequest()`.
  - OpenAPI: configured in `spring/config/OpenApiConfig.java` (controller package scanning for docs).

- **Files to inspect first**
  - Controller: [src/main/java/evrentan/examples/springbootprojectexample/controller/CustomerController.java](src/main/java/evrentan/examples/springbootprojectexample/controller/CustomerController.java)
  - Mapper: [src/main/java/evrentan/examples/springbootprojectexample/mapper/CustomerMapper.java](src/main/java/evrentan/examples/springbootprojectexample/mapper/CustomerMapper.java)
  - AOP logging: [src/main/java/evrentan/examples/springbootprojectexample/aspect/ApiLogAspect.java](src/main/java/evrentan/examples/springbootprojectexample/aspect/ApiLogAspect.java)
  - Config: [src/main/java/evrentan/examples/springbootprojectexample/spring/config/CommonConfig.java](src/main/java/evrentan/examples/springbootprojectexample/spring/config/CommonConfig.java)

If you want expansion on testing examples (mocking `RequestContextHolder` / `CommonUtility`), MapStruct unit-test patterns, or CI expectations, tell me which to add.
# Copilot / AI Agent Instructions — spring-boot-project-example

Purpose: give an AI coding agent the essential, actionable context to be productive in this repository.

- **Big picture**: This is a small Spring Boot service (Spring Boot 2.6.2, Java 17, Maven) that exposes REST APIs for `Customer` entities persisted in MongoDB. Key layers:
  - Controllers: `src/main/java/**/controller/*` (e.g. `CustomerController`) — REST endpoints and `@ApiLogger` usage.
  - Services: `impl/*` implementing `ICustomerService` (transactional boundaries live here).
  - Repositories: Spring Data `MongoRepository` interfaces in `repository/*` (e.g. `CustomerRepository`, `ApiLogRepository`).
  - Mappers: MapStruct mappers in `mapper/*` (componentModel = "spring" and strict unmapped policies).
  - Config: `spring/config/CommonConfig`, `WebConfig`, `OpenApiConfig` — component/entity/repository scanning, `customObjectMapper` bean, and OpenAPI groups.

- **Why things are structured this way**:
  - MapStruct is used for DTO/entity conversion with strict mapping policies (unmapped -> ERROR) so generated mappings must cover all fields or be explicitly ignored.
  - MongoDB is the persistence store (look at `application.properties` and `EnableMongoRepositories` in `CommonConfig`).
  - AOP-based API logging: controller methods annotated with `@ApiLogger` are wrapped by `ApiLogAspect` and persisted to Mongo via `ApiLogRepository`.

- **Build / run / test (exact commands)**
  - Build & compile (with annotation processing): `mvn -U clean package` (MapStruct requires annotation processing; ensure IDE enables it).
  - Run locally: `mvn spring-boot:run` or run `SpringBootProjectExampleApplication` main class in the IDE.
  - Run tests: `mvn test`.
  - Javadoc: `mvn javadoc:javadoc` (documented in README).

- **Local environment requirements**
  - Java 17 and Maven (project properties set java.version=17).
  - MongoDB running (defaults from `src/main/resources/application.properties`: host `localhost`, port `27017`, db `spring-boot-project-example`).
  - IDE: enable Lombok and annotation processing for MapStruct.

- **Key repository-specific conventions / patterns**
  - Mappers: `CustomerMapper` uses `@Mapper(componentModel="spring")` and `@MapperConfig(unmappedTargetPolicy = ReportingPolicy.ERROR)` — failing to map fields will cause compile-time errors. Use `@BeanMapping(ignoreUnmappedSourceProperties=...)` or `@Mapping(target=..., ignore=true)` where appropriate.
  - DTOs vs Entities: DTOs live in `dto/`, entities in `entity/`; mapping methods often ignore entity `id` on create (`@Mapping(target = "id", ignore = true)`). Check `CustomerMapper#setEntityId` (`@AfterMapping`) which preserves `id` when present.
  - API logging: methods annotated with `@ApiLogger` (see `annotation/ApiLogger.java`) are intercepted by `aspect/ApiLogAspect.java`. The aspect expects an HTTP request to be available via `CommonUtility.getHttpServletRequest()` and writes `ApiLogEntity` via `ApiLogRepository`.
  - Bean naming: a `CustomObjectMapper` bean is declared in `CommonConfig` with name `customObjectMapper` and is injected by `@Qualifier("customObjectMapper")` in `ApiLogAspect`.
  - Repositories: interfaces extend `MongoRepository<Entity, String>` and are auto-scanned via `@EnableMongoRepositories` in `CommonConfig`.

- **Integration points / external dependencies**
  - MongoDB: required for normal runs; tests may use an embedded Mongo or mocks — there is no embedded-mongo dependency configured by default.
  - OpenAPI / Swagger UI via `springdoc-openapi-ui` — configured in `OpenApiConfig` and scans controllers under `evrentan.examples.springbootprojectexample.controller`.
  - MapStruct and Lombok: compile-time code generation; ensure annotation processing.

- **Common pitfalls for PRs and code changes**
  - Changing DTO/entity fields requires updating MapStruct mappers (or adding ignore mappings) or the build will fail.
  - Adding controller methods that should be logged must include `@ApiLogger` if they should be persisted to API logs.
  - Tests interacting with the AOP logging layer need to provide or mock an `HttpServletRequest` accessible via `RequestContextHolder` or stub `CommonUtility.getHttpServletRequest()`.

- **Files to inspect for implementation examples**
  - Controller example: [src/main/java/evrentan/examples/springbootprojectexample/controller/CustomerController.java](src/main/java/evrentan/examples/springbootprojectexample/controller/CustomerController.java)
  - Service + transactions: [src/main/java/evrentan/examples/springbootprojectexample/impl/CustomerServiceImpl.java](src/main/java/evrentan/examples/springbootprojectexample/impl/CustomerServiceImpl.java)
  - Mapper example: [src/main/java/evrentan/examples/springbootprojectexample/mapper/CustomerMapper.java](src/main/java/evrentan/examples/springbootprojectexample/mapper/CustomerMapper.java)
  - AOP logging: [src/main/java/evrentan/examples/springbootprojectexample/aspect/ApiLogAspect.java](src/main/java/evrentan/examples/springbootprojectexample/aspect/ApiLogAspect.java)
  - Bean + repo scanning: [src/main/java/evrentan/examples/springbootprojectexample/spring/config/CommonConfig.java](src/main/java/evrentan/examples/springbootprojectexample/spring/config/CommonConfig.java)

If a section above is unclear or you want the instructions expanded (for example, adding recommended test patterns, example unit tests that mock `RequestContextHolder`, or a CI matrix), tell me which piece to expand and I will update this file.
