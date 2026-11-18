# Shop-Nest

Microservices-based e-commerce backend in Java and Spring Boot: JWT and OAuth 2.0 (Google, GitHub) login, product catalog, AWS deployment, and an MCP server for AI access to catalog data.

## Overview

Shop-Nest is a backend e-commerce platform split into independent services. Users can sign in with a password or with **Google or GitHub through OAuth 2.0**, and every login ends with the same **JWT** for later API calls. It also supports **role-based access (Admin / User)**, product browsing and search, and LLM-generated product pitches. A separate MCP server exposes catalog data to AI tools.

## Repositories

| Service | Description | Repo |
|---|---|---|
| **User Authentication Service** | Registration, password and OAuth 2.0 login, JWT issuing and role-based authorization | [User Auth](https://github.com/abhijeet-00/UserAuthService) |
| **Product Catalog Service** | Product browsing, categories, search and product management | [Product Catalog](https://github.com/abhijeet-00/ProductCatalogService) |
| **Catalog MCP Server** | Exposes product retrieval tools to AI clients via Model Context Protocol | [MCP Catalog](https://github.com/abhijeet-00/MCP-Catalog) |

## Architecture

```mermaid
flowchart LR
    Client --> Auth[User Authentication Service]
    Client --> Catalog[Product Catalog Service]
    Auth --> IdP["Google / GitHub (OAuth 2.0)"]
    Auth --> DB1[(MySQL)]
    Catalog --> DB2[(MySQL)]
    Catalog --> Ext[External product API]
    Catalog --> LLM[Groq LLM API]
    MCP[Catalog MCP Server] --> Catalog
    AI[AI clients] --> MCP
```

## Authentication

Users can log in in two ways:

1. **Password login:** credentials are verified by the User Authentication Service, which issues a JWT.
2. **OAuth 2.0 login:** the user signs in with **Google** or **GitHub**. Once the provider authenticates the user, the service issues the **same JWT** as for password login.

The **User Authentication Service** handles both login methods and issues the JWT. The **Product Catalog Service** doesn't authenticate users itself. It only validates the JWT sent with each API call.

## Access Control

Read endpoints are public; all write endpoints require a valid JWT.

| Operation | Public (no token) | User | Admin |
|---|---|---|---|
| Read (GET) | Yes | Yes | Yes |
| Create (POST) | No | Yes | Yes |
| Update (PUT/PATCH) | No | Yes | Yes |
| Delete (DELETE) | No | No | Yes |

## Key Features

- **Password and OAuth 2.0 login** (Google, GitHub) that both issue the same **JWT**
- **Role-based authorization** (Admin / User) using Spring Security
- **Public read APIs** for product browsing, categories and search; **token-protected** create, update and delete
- **Product management** for authenticated users, with delete restricted to Admins
- Catalog data **seeded from an external API**
- **LLM-generated product pitches** using the Groq API
- **Custom MCP server** exposing product retrieval tools to AI clients

## Tech Stack

- **Language and framework:** Java 17, Spring Boot, Spring Security, Spring Data JPA, Hibernate
- **Auth:** OAuth 2.0 (Google, GitHub), JWT
- **Database:** MySQL (AWS RDS in higher environments)
- **Testing:** JUnit, Mockito (unit and integration tests)
- **Build:** Maven
- **Cloud and CI/CD:** AWS Elastic Beanstalk, AWS RDS, GitHub Actions
- **AI:** Groq LLM API, Model Context Protocol (MCP)

## Testing

Both the User Authentication Service and the Product Catalog Service have **unit tests** and **integration tests**, written with **JUnit** and **Mockito**. Unit tests run as a step after the build in each service's pipeline.

**Unit tests**

User Authentication Service

```bash
mvn -B test -Dtest=BcryptAuthServiceTests,JwtServiceTests -Dsurefire.failIfNoSpecifiedTests=false --file pom.xml
```

Product Catalog Service

```bash
mvn -q test -Dtest='GroqPitchServiceTests,ProductInventoryServiceTests,DummyProductServiceTests,CategoryServiceTests'
```

**Integration tests** (run in each service)

```bash
.\mvnw.cmd -B test -Dtest='*IntegrationTests'
```

On Linux or macOS, use `./mvnw -B test -Dtest='*IntegrationTests'`.

## Deployment

Both services are deployed on **AWS Elastic Beanstalk**, using **RDS MySQL** in higher environments. **GitHub Actions** builds, runs the unit tests and deploys automatically on every push to **main** branch.

## Development Approach

Built with an AI-assisted workflow using **GitHub Copilot** and **OpenCode** for code generation and refactoring. I designed the service boundaries and security rules and reviewed all generated code. **Context7** and **Notion MCP servers** were used to pull dependency documentation and keep project notes.

## Status

In progress: Docker and Kafka-based messaging.

## Author

Abhijeet Singh | [LinkedIn](https://www.linkedin.com/in/abhijeet-singh-33b13821a/) | [GitHub](https://github.com/abhijeet-00)
