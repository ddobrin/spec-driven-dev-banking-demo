# Building the Banking CIF Application

This application is distributed as a specification. To build it, you should use an AI coding agent or follow these steps manually.

## Agent Prompt

Copy and paste the following prompt into your coding agent (e.g., Gemini, Claude, Cursor):

```text
Implement a Banking CIF (Customer Information Facility) Microservice using the Techstack described in TECHSTACK.md

1. **Analyze the Requirements:**
   - Read `SPEC.md` for the architecture, API endpoints, and design principles.
   - Read `ddl.md` to understand the PostgreSQL database schema.
   - Read `test.yaml` to understand the required API test scenarios.

2. **Setup the Project:**
   - Create a new Spring Boot 4 project.
   - Ensure that Spring Boot 4.0.2 is strictly specified in the pom.xml
   - Configure dependencies for Web, JPA (Hibernate), PostgreSQL Driver, and Validation.
   - Ensure Java 21 is configured in `pom.xml` or `build.gradle`.

3. **Implement the Data Layer:**
   - Create JPA Entities mapping to the tables in `ddl.md` (`Customer`, `Account`, `Product`, `Transaction`).
   - Use UUIDs for primary keys as defined.
   - Implement Repositories.

4. **Implement the Business Logic:**
   - Create Service classes for `Customer`, `Account`, and `Transaction`.
   - Implement the logic for CRUD operations.
   - Ensure business rules (e.g., insufficient funds check) are handled.
   - Use Java POJO/Constructors/Records and do not use Lombok

5. **Implement the API Layer:**
   - Create REST Controllers matching the `SPEC.md` endpoints.
   - Use DTOs for request/response bodies (do not expose Entities directly).
   - Implement `GlobalExceptionHandler` to match the error format in `SPEC.md`.

6. **Testing:**
   - Create integration tests (using `@SpringBootTest` and `MockMvc` or `TestContainers`) that verify the scenarios in `test.yaml`.
   - Ensure all tests pass.

7. **Documentation:**
   - Generate a `usage.md` describing how to run the app and call the APIs.
```

## Manual Installation

If you are building this manually:

1.  Initialize a Spring Boot project.
2.  Set up a local PostgreSQL database and run the script from `ddl.md`.
3.  Write the code following the `SPEC.md`.
4.  Write tests that assert the behaviors defined in `test.yaml`.
