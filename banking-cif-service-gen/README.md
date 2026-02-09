# Banking CIF Application

A specification-first implementation of a Banking Customer Information Facility (CIF) microservice.

## Overview

This repository contains the **specification** for a Banking CIF application. It defines the behavior, API contract, and testing requirements for a microservice that manages:
1.  **Customers (Person):** Identity and KYC status.
2.  **Financial Applications (Accounts):** Product instances like Savings or Checking accounts.
3.  **Transactions:** Financial movements (Deposits, Withdrawals).

The goal is to implement this specification using **Java 25** and **Spring Boot 4**.

## Project Structure

This project follows a "Ghost App" pattern, where the code is generated based on a strict spec.

-   `SPEC.md`: The complete technical specification, including API endpoints and data model.
-   `test.yaml`: Language-agnostic test cases defining the expected behavior of the API.
-   `INSTALL.md`: Instructions for an AI agent (or human developer) to build the application.
-   `ddl.md`: The PostgreSQL schema definition.
-   `TECHSTACK.md`: A sample tech stack to be used when generating the codebase

## Technology Requirements

-   **Language:** Java 25 (Preview/Latest features)
-   **Framework:** Spring Boot 4
-   **Build Tool:** Maven or Gradle
-   **Database:** PostgreSQL

## How to Build

Refer to `INSTALL.md` for instructions on how to prompt an AI agent to generate the implementation.
