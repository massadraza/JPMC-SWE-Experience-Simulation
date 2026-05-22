# JPMC Virtual Software Engineering Experience — Project Midas

This repository contains my work for the **JPMorgan Chase & Co. Forage Virtual Software Engineering Program**. The project simulates real-world backend engineering tasks on **Midas**, a financial transaction processing system built with Spring Boot and Apache Kafka.

---

## Tech Stack

- **Java 17** with Spring Boot
- **Apache Kafka** (embedded for testing via `spring-kafka-test`)
- **H2 in-memory database** with Spring Data JPA
- **Maven** for build and dependency management
- **JUnit 5** for testing

---

## Project Structure

```
forage-midas/
├── src/
│   ├── main/java/com/jpmc/midascore/
│   │   ├── component/
│   │   │   ├── DatabaseConduit.java       # JPA abstraction layer for user persistence
│   │   │   ├── IncentiveService.java      # HTTP client for external incentive API
│   │   │   └── TransactionListener.java   # Kafka consumer — core transaction logic
│   │   ├── controller/
│   │   │   └── BalanceController.java     # REST endpoint: GET /balance?userId=
│   │   ├── entity/
│   │   │   └── UserRecord.java            # JPA entity representing a user account
│   │   ├── foundation/
│   │   │   ├── Balance.java               # Response model for balance queries
│   │   │   ├── Incentive.java             # Response model for incentive API
│   │   │   └── Transaction.java           # Kafka message model
│   │   └── repository/
│   │       └── UserRepository.java        # Spring Data JPA repository
│   └── test/java/com/jpmc/midascore/
│       ├── TaskOneTests.java
│       ├── TaskTwoTests.java
│       ├── TaskThreeTests.java
│       ├── TaskFourTests.java
│       └── TaskFiveTests.java
└── pom.xml
```

---

## Tasks Completed

### Task 1 — Application Bootstrapping
**Goal:** Get the Spring Boot application to start without errors.

Set up the foundational Spring Boot configuration so the application context loads cleanly. Verified by running `TaskOneTests`, which boots the application and prints a numeric output sequence derived from powers of integers.

**Key concept:** Spring Boot auto-configuration, dependency injection, and application startup verification.

---

### Task 2 — Kafka Integration (Listening to Transactions)
**Goal:** Connect the application to an embedded Kafka broker and receive transaction messages.

Implemented `TransactionListener`, a `@KafkaListener` component that consumes `Transaction` messages from a configured Kafka topic. Used a debugger to inspect live incoming transaction data during the test run.

**Key concept:** Event-driven architecture with Apache Kafka; deserializing JSON messages into typed Java objects.

---

### Task 3 — Transaction Processing Logic
**Goal:** Process transactions by updating sender and recipient balances in the database.

Extended `TransactionListener` to:
1. Look up sender and recipient `UserRecord` entities via `DatabaseConduit`
2. Validate that the sender exists, the recipient exists, and the sender has sufficient funds
3. Deduct the transaction amount from the sender and credit the recipient
4. Persist both updated records

Verified by checking a specific user's (Waldorf's) balance after a batch of transactions.

**Key concept:** Database persistence with Spring Data JPA; business logic validation.

---

### Task 4 — Incentive Integration
**Goal:** Augment transactions with bonus amounts from an external incentive microservice.

Implemented `IncentiveService`, which calls an external REST API (`transaction-incentive-api.jar`) using `RestTemplate`. For each valid transaction, the incentive amount (if any) is added on top of the transferred amount and credited to the recipient.

Verified by checking a specific user's (Wilbur's) final balance after transactions including incentive bonuses.

**Key concept:** Microservice communication; integrating external HTTP APIs into a transactional flow.

---

### Task 5 — REST API for Balance Queries
**Goal:** Expose a REST endpoint to query user balances programmatically.

Implemented `BalanceController` with a `GET /balance?userId={id}` endpoint that looks up a user in the database and returns their current balance as a `Balance` response object. Returns `0` for unknown users.

Verified end-to-end: transactions are processed via Kafka, balances are updated, and then queried via HTTP for all users — the final output is submitted to complete the simulation.

**Key concept:** RESTful API design with Spring MVC; end-to-end integration of Kafka consumers and HTTP endpoints.

---

## Running the Project

### Prerequisites
- Java 17+
- Maven 3.8+
- The `transaction-incentive-api.jar` service running on port `8080` (included in `services/`)

### Start the Incentive API
```bash
java -jar forage-midas/services/transaction-incentive-api.jar
```

### Run all tests
```bash
cd forage-midas
./mvnw test
```

### Run a specific task test
```bash
./mvnw test -Dtest=TaskFiveTests
```

---

## Key Concepts Practiced

| Concept | Where Applied |
|---|---|
| Spring Boot application setup | Task 1 |
| Apache Kafka consumer (`@KafkaListener`) | Task 2 |
| Spring Data JPA (entities, repositories) | Task 3 |
| Input validation & business rules | Task 3 |
| REST client with `RestTemplate` | Task 4 |
| Microservice integration | Task 4 |
| Spring MVC REST controller | Task 5 |
| End-to-end integration testing | Tasks 2–5 |

---

## Author

Massad Raza — JPMC Forage Virtual SWE Experience, May 2026
