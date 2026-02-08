## Technology Stack

- **Language:** Java 21
- **Framework:** Spring Boot 4.0.2
- **Spring Boot profiles:** dev, test, prod
- **Auto-configuration:** use Spring boot autoconfiguration
- **Database:** PostgreSQL (Schema defined in `ddl.md`) for prod, H2 for dev, Postgres Testcontainer for running tests in test profile
- **TestContainers:** Use version 1.20.4 for database testing - use a Postgres container
- **JSON Library:** Use Jackson 3 for JSON processing
- **Architecture:** Layered (Controller, Service, Repository)