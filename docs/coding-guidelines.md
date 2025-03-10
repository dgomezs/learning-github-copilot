# Project Instructions for GitHub Copilot

## Project Overview
This is a Java-based monorepo containing multiple microservices. Each service follows clean architecture principles with a clear separation of commands and queries (CQRS). The project adopts API-first design using OpenAPI specifications and employs event-driven architecture with Kafka. The project aims to be maintainable, testable, and scalable.

## GitHub Copilot Guidelines
### Code Generation Best Practices
- Ensure Copilot-generated code adheres to project coding standards.
- Always review, test, and refactor suggestions before committing.
- Use inline comments to guide Copilot when generating complex logic.

## Technology Stack
- Java 21 (LTS)
- Quarkus framework
- RESTful API design with OpenAPI/Swagger
- Apache Kafka for event streaming
- AsyncAPI for event-driven API specifications
- Maven for dependency management
- JUnit 5 for testing
- PostgreSQL for database

## Monorepo Structure
- Organized with multiple services in the apps folder
- Shared libraries and utilities in the libs folder
- Common configuration in the config folder


## Coding Standards
- Follow Google Java Style Guide
- Use meaningful variable and method names
- Include Javadoc for all public methods and classes
- Implement proper error handling
- Use design patterns where appropriate
- Keep methods small and focused on a single responsibility
- Use Project Lombok to reduce boilerplate code:
  - Use `@Data` for data classes
  - Use `@Builder` for builder pattern
  - Use `@Slf4j` for logging
  - Use `@RequiredArgsConstructor` for dependency injection
  - Prefer constructor injection with `final` fields
  - Use `@Value` for immutable classes
  - Avoid `@ToString` on entity classes to prevent circular references

## Architecture
- Follow clean architecture principles
- Implement Command Query Responsibility Segregation (CQRS)
- Separate concerns between layers:
  - API/Controller (External interfaces defined by OpenAPI specs)
  - Application (Use cases implementing commands and queries)
  - Domain (Business logic and entities)
  - Infrastructure (External services, databases, Kafka producers/consumers)

## Command and Query Separation
- Commands: Operations that change state and don't return data
- Queries: Operations that return data but don't change state
- Each use case should be a dedicated command or query class, all organized within the usecases directory
- Follow naming convention: [Action][Entity][Command/Query] (e.g., CreateUserCommand, GetUserByIdQuery)

## Security Considerations
- Implement proper input validation
- Use parameterized queries to prevent SQL injection
- Protect sensitive data through proper handling and encryption
- Implement request rate limiting to prevent DoS attacks

## Performance Guidelines
- Optimize database queries
- Implement caching where appropriate
- Consider asynchronous processing for long-running tasks
- Profile and optimize critical code paths
- Optimize Kafka topic partitioning for parallel processing

## Database Migration Strategy
- Use Liquibase for database schema evolution and versioning
- Store changelog files in a dedicated directory structure:
  - `src/main/resources/db/changelog/` for main changelog files
  - Use semantic versioning for changelog files (e.g., `v1.0.0.xml`, `v1.1.0.xml`)
- Organize changes by type and entity:
  - `db/changelog/changes/tables/` - Table creation and modifications
  - `db/changelog/changes/sequences/` - Sequence definitions
  - `db/changelog/changes/data/` - Reference/seed data
  - `db/changelog/changes/constraints/` - Foreign keys and constraints
  - `db/changelog/changes/indexes/` - Index creation
- Follow naming conventions:
  - `YYYYMMDDHHMMSS_short-description.xml` for change files
- Include descriptive comments for each changeset
- Implement rollback procedures for all changes
- Test migrations in CI pipeline
- Separate DDL (Data Definition Language) from DML (Data Manipulation Language)
- Run migrations automatically during application startup in development
- Use controlled migration execution in production environments
- Include database versioning checks during application startup
- Generate database documentation from Liquibase changesets

## Dependency Management & Tooling
- Use Maven BOM (Bill of Materials) to ensure consistent dependency versions
- Favor library versions that align with LTS support
- Scan dependencies for vulnerabilities (e.g., OWASP Dependency Check)
- The root pom should contain the versions of the libraries used by the apps and libs
- Ensure builds pass with `mvn verify` before pushing changes
- Define container classes in `src/test/java/<package>/test/containers/`

## Observability Stack
- Implement the three pillars of observability: logs, metrics, and traces
- Use OpenTelemetry as the vendor-agnostic observability framework
- Export telemetry data to your preferred backends (Jaeger, Prometheus, Elasticsearch, etc.)
- Follow W3C trace context specification for consistent cross-service tracing

## Logging
- Use structured logging with JSON format (Logback + SLF4J)
- Set appropriate log levels (`DEBUG`, `INFO`, `WARN`, `ERROR`)
- Include trace and span IDs in log entries for correlation
- Centralize logs in a searchable platform (e.g., Elasticsearch)

## Tracing & Metrics with OpenTelemetry
- Implement distributed tracing across all services
- Configure OpenTelemetry SDK in each service
- Automatically instrument frameworks and libraries where possible
- Add custom instrumentation for business-critical operations
- Create custom spans for important business transactions
- Define and collect custom metrics for business KPIs
- Expose Prometheus endpoint in each service
- Track the four golden signals: latency, traffic, errors, and saturation
- Set up dashboards for visualizing metrics and traces

## Error Handling & Resilience
- Define a global exception handling strategy
- Use retry mechanisms for transient failures
- Implement graceful degradation (e.g., circuit breakers with Resilience4j)
- Set up alerts based on error rates and SLOs



## Example Service Structure
apps/
├── service-a/
│   ├── api/
│   │   ├── rest/
│   │   │   └── openapi.yaml
│   │   └── events/
│   │       └── asyncapi.yaml
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/example/servicea/
│   │   │   │       ├── api/
│   │   │   │       │   ├── rest/
│   │   │   │       │   └── events/
│   │   │   │       │       ├── producers/
│   │   │   │       │       └── consumers/
│   │   │   │       ├── application/
│   │   │   │       │   └── usecases/
│   │   │   │       │       ├── CreateUserCommand.java
│   │   │   │       │       ├── UpdateUserCommand.java
│   │   │   │       │       ├── GetUserByIdQuery.java
│   │   │   │       │       └── ListUsersQuery.java
│   │   │   │       ├── domain/
│   │   │   │       │   ├── model/
│   │   │   │       │   ├── events/
│   │   │   │       │   └── services/
│   │   │   │       ├── infrastructure/
│   │   │   │       │   ├── config/
│   │   │   │       │   │   ├── OpenTelemetryConfig.java
│   │   │   │       │   │   └── KafkaConfig.java
│   │   │   │       │   ├── persistence/
│   │   │   │       │   ├── kafka/
│   │   │   │       │   │   ├── producers/
│   │   │   │       │   │   └── consumers/
│   │   │   │       │   ├── external/
│   │   │   │       │   └── observability/
│   │   │   │       │       ├── CustomMetrics.java
│   │   │   │       │       ├── TracingAspect.java
│   │   │   │       │       └── MetricsService.java
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── otel-collector-config.yaml
│   │       └── avro/
│   │           └── user-events.avsc
│   │   └── test/
│   │       └── java/
│   │           └── com/example/servicea/
│   │               ├── usecases/
│   │               │   ├── CreateUserCommandTest.java
│   │               │   ├── UpdateUserCommandTest.java
│   │               │   ├── GetUserByIdQueryTest.java
│   │               │   └── ListUsersQueryTest.java
│   │               ├── adapters/
│   │               │   ├── persistence/
│   │               │   ├── api/
│   │               │   │   ├── rest/
│   │               │   │   └── events/
│   │               │   │       ├── producers/
│   │               │   │       └── consumers/
│   │               │   └── external/
│   │               └── contracts/
│   │                   ├── consumer/
│   │                   │   ├── ServiceBContractTest.java
│   │                   │   └── ServiceCContractTest.java
│   │                   ├── provider/
│   │                   │   ├── ProviderStateHandler.java
│   │                   │   ├── ServiceAContractVerificationTest.java
│   │                   │   └── pact-verifier.properties
│   │                   └── schemas/
│   │                       ├── openapi-to-pact-mappings.json
│   │                       └── asyncapi-to-pact-mappings.json
│   ├── pact/
│   │   ├── consumer/
│   │   │   └── service-a-service-b.json
│   │   └── provider/
│   │       └── service-c-service-a.json
│   └── pom.xml
├── service-b/
│   └── ...
└── service-c/
    └── ...
libs/
├── common/
├── api-clients/
│   ├── rest/
│   └── events/
├── contract-testing/
│   ├── openapi-pact-generator/
│   ├── asyncapi-pact-generator/
│   └── contract-test-utils/
└── asyncapi-tools/
    ├── generators/
    └── validators/
config/
├── shared-config/
├── kafka/
│   ├── topics.yaml
│   └── schema-registry.yaml
├── pact/
│   ├── broker-config.yml
│   └── verification-rules.json
└── asyncapi/
    └── common-message-library.yaml



