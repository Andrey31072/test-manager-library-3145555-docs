# Traceability Matrix

## Spec Task Mapping

| Task | Repository | Evidence |
|---|---|---|
| T-01 Skeleton and health | `api`, `infra` | `GET /api/v1/health` returns `{"status":"ok"}`; container starts. |
| T-02 Initial migration | `api` | Six tables, constraints, indexes, schema `library`, fixed categories, no defaults. |
| T-03 Value objects | `api` | Tests for ISBN, quantity, and date range. |
| T-04 Book domain | `api` | Tests for withdraw, restock, logical deletion. |
| T-05 Loan domain | `api` | Tests for one to three lines, no duplicates, 14-day due date, calculated status. |
| T-06 Member and User domain | `api` | Tests for username normalization and valid roles. |
| T-07 Auth use cases | `api` | Fake-port tests for login and librarian creation. |
| T-08 Book use cases | `api` | Fake-port tests for create, search, edit, delete, cover. |
| T-09 Loan use case | `api` | Tests for limits, overdue block, stock conflicts. |
| T-10 Return and overdue | `api` | Tests for return once and overdue listing. |
| T-11 Most-borrowed report | `api` | Frozen values remain after catalog rename. |
| T-12 Persistence adapters | `api` | PostgreSQL integration tests. |
| T-13 Loan persistence concurrency | `api` | Last-copy concurrent request test. |
| T-14 Security adapters | `api` | Hash/token tests and no secret in responses. |
| T-15 Cover adapter | `api` | JPEG/PNG <= 2 MB accepted, oversize rejected. |
| T-16 HTTP adapter | `api` | Contract tests for routes, roles, status codes, and error body. |
| T-17 Composition and bootstrap | `api` | Empty database boots and admin can log in from env variables. |
| T-18 Database checks | `api`, `docs` | `ck_book_total_positive` and `ck_user_role` are present and documented. |
| T-19 Architecture tests | `api` | PHPUnit checks for dependency direction. |
| T-20 Docker Compose | `infra` | One command starts empty database, API, and frontend. |
| T-21 Demo seeder | `tool` | API-only seeder creates data and overdue/report evidence. |
| T-22 React app | `app` | Manual flow: login, catalog, members, lend, return, overdue, report. |
| T-23 Static page | `page` | Opens without API and renders on mobile and desktop. |

## Delivery Strategy

1. Complete and validate English documentation before product code.
2. Deliver each coherent story slice through `Hu-NN-dev`, `Hu-NN-qa`, and `Hu-NN-main` branches.
3. Cherry-pick the reviewed story commit from the development branch into QA, then from QA into main.
4. Keep commits small and preserve the same story intent across all affected repositories.
5. Do not force five artificial business stories into repositories that have fewer responsibilities; use five traceable delivery slices while retaining the ten canonical stories unchanged.

## Cross-Repository Alignment

- The API owns business rules and the database schema.
- The app and tool depend on the same `/api/v1` contract.
- Infra injects environment variables but does not own schema.
- Page is public and static, with no API dependency.
