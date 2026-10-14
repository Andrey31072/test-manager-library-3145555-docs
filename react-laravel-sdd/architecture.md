# React and Laravel Architecture

## Repository Responsibilities

| Repository | Responsibility | Must not do |
|---|---|---|
| `docs` | Preserve reference specs and this English adaptation | Runtime behavior |
| `api` | Laravel backend, domain rules, migrations, contract tests, auth, storage adapters | React UI, Docker-owned DDL |
| `app` | React operational app for internal users | Business rule enforcement beyond client validation |
| `page` | Static public presentation site | API calls |
| `infra` | Docker Compose, network, volumes, empty PostgreSQL engine | `CREATE TABLE`, category seed SQL, app business code |
| `tool` | Demo data seeder through the API | Direct SQL writes |

## Backend Layering

The Laravel backend follows the same hexagonal dependency direction as the reference spec.

```text
app/
  Domain/                 pure entities, value objects, business exceptions
  Application/
    Ports/                repositories, clock, hasher, token issuer, cover storage
    Services/             one use case per service
  Infrastructure/
    Persistence/          Eloquent/query-builder adapters
    Security/             Laravel Hash and JWT adapters
    Storage/              local cover adapter
  Http/
    Controllers/Api/V1    contract endpoints
    Middleware            bearer auth and role checks
    Requests              input validation only
bootstrap/app.php         single composition point for service bindings
database/migrations       all DDL and category seed
tests/                    domain, application, contract, architecture, concurrency
```

Dependency rule:

- Domain imports only PHP base types and local domain classes.
- Application imports Domain and its own ports.
- Infrastructure implements Application ports.
- HTTP translates requests and domain/application errors to the unified API error body.
- Composition binds ports to adapters in one place.

## Frontend Shape

The React app consumes only `/api/v1` and keeps no business source of truth.

Required screens:

- Login.
- Catalog search/detail and admin book management.
- Member search/detail and creation.
- Loan registration with one to three books.
- Return flow from loan detail.
- Overdue operational list.
- Most-borrowed report for admin.

Client-side validation improves ergonomics, but server responses remain authoritative.

## Static Page Shape

The public page is a standalone static site. It presents the project, scope, stack, roles, and demo flow. It must not call the API.

## Tool Shape

The seeder is a Node CLI. It logs in through `/auth/login`, creates demo librarians if needed, creates at least 40 books and 10 members, creates at least 15 loans, and produces exactly three overdue lines for the evidence path.

## Security

- Bearer token required except `POST /auth/login` and `GET /health`.
- Token contains `sub` and `role`, expires in 28,800 seconds.
- No personal data in token.
- Passwords are hashed through a port and never returned.
- CORS allows only configured frontend origin.
- Cover upload validates content as JPEG/PNG and maximum size 2 MB.

## Time

All stored instants are UTC. Application services receive time through a `Clock` port so overdue and report behavior can be tested deterministically.
