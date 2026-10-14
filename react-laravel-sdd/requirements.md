# Requirements and Trap Register

## Product

Manager Library is a small library management system for catalog books, members, loans, returns, overdue operational lists, and the most-borrowed report.
The product is operated by authenticated internal users and presented publicly through a static page.

## Actors

| Actor | Role in system | Authentication |
|---|---|---|
| Administrator | Maintains books, members, librarians, and reports | Username and password for bearer token |
| Librarian | Registers loans and returns, reads catalog, members, and overdue items | Username and password for bearer token |
| Member | Person borrowing books | Data record only; never logs in |

The initial administrator is provisioned from environment variables. The API must not grant the `admin` role at runtime.

## In Scope

- Book catalog with optional cover.
- Exactly five fixed categories: Novela, Ciencia, Tecnología, Historia, Infantil.
- Member creation and search.
- Loans with one to three lines.
- One-line-at-a-time returns.
- Overdue lines.
- Most-borrowed report.
- Login, bearer authentication, and role authorization.

## Out of Scope

Fines, payments, reservations, renewals, email/SMS, bulk import, multi-branch lending, publisher, publication year, language, member login, report persistence, audit tables, and stored loan status.

## Business Rules

| Rule | Requirement |
|---|---|
| BR-1 | `available_copies` is always between 0 and `total_copies`. |
| BR-2 | Loan term is 14 days. A member may have at most three unreturned books. |
| BR-3 | A member with any overdue unreturned item cannot borrow more books. |
| BR-4 | Loan item title, ISBN, and category name are frozen at loan time. |
| BR-5 | Loans and returns are never deleted. Books use logical deletion. |
| BR-6 | Dates are UTC instants. Code, routes, and columns are English. User-facing messages are Spanish. |

## User Stories and Acceptance Summary

| Story | Required behavior |
|---|---|
| US-01 Login | Valid credentials return an 8-hour token; invalid credentials return generic 401; all endpoints except login and health require token. |
| US-02 Librarian creation | Admin creates only `librarian` users; usernames are lowercase, trimmed, unique; `admin` through API returns 422. |
| US-03 Book management | Admin creates, updates, uploads/deletes cover, and logically deletes active books; ISBN is 13 digits and unique among active books; open loans block deletion. |
| US-04 Book search | Authenticated search by partial title/author, category, active only, title order, paginated with total. |
| US-05 Member management | Admin and librarian create members; document number is unique; members are searched and listed; members are not deleted. |
| US-06 Loan creation | One member, authenticated registering user, one to three unique books, atomic stock decrement, member limit and overdue checks, frozen values, no edit/delete. |
| US-07 Return | Return one line, set `returned_at`, increment stock once, never exceed total, closed status when all returned. |
| US-08 Loan read/list | Detail includes lines and calculated status; list filters by date range, status, member, desc date, pagination. |
| US-09 Overdue list | Unreturned items with `due_at` before now, days late, sorted by highest days late first. |
| US-10 Most-borrowed report | Admin only; inclusive date range; grouped by frozen `book_id + book_title + category_name`; no members or librarians. |

## Non-Functional Requirements

| Requirement | Evidence |
|---|---|
| Book search under 300 ms with 5,000 books | Load check with demo seeder data. |
| Last-copy concurrency | Two simultaneous loans for one available copy: exactly one succeeds and the other receives 409. |
| Password secrecy | Passwords and hashes never appear in logs or responses. |
| Unified HTTP error body | Contract tests assert the same structure for all errors. |
| One-command startup | Infra README and Docker Compose start empty database, API, and frontend. |

## Trap and Ambiguity Register

| Item | Resolution |
|---|---|
| Two specs exist, Python and .NET | They describe the same product. Technology-specific choices are not authoritative for this challenge. |
| The reference docs mention Python/.NET architecture tools | Translate the architectural intent to Laravel and PHPUnit; do not implement Python or .NET. |
| `data-model.md` says no column defaults | Laravel migrations must avoid database defaults, including timestamps. |
| Laravel normally adds `created_at` and `updated_at` | Disable model timestamps and do not create audit columns. |
| `user` is a reserved SQL word | Create `library."user"` and configure Laravel table names explicitly. |
| `GET /books/{id}/cover` is mentioned in prose but missing from the endpoint table | Implement it because cover rendering depends on it and the contract text explicitly names it. |
| T-18 asks to add checks marked pending | This implementation may include `ck_book_total_positive` and `ck_user_role` directly in the initial Laravel migration and document that evidence. |
| Error messages must be Spanish while documentation is now English | Keep docs in English; API messages returned to users remain Spanish. |
| Infra must not own DDL | No `CREATE TABLE` or schema seed SQL in infra. DDL lives in the Laravel migration. |
| ADR-002 requires a version witness but the authoritative model has no version column | The data model wins. Use an atomic conditional stock update and verify the affected row count; do not add a column and do not use a long-lived pessimistic lock. |
| The demo requires exactly three overdue lines, but the public API cannot create historical loans | This is an unresolved source contradiction. The tool must not write SQL or invent an endpoint. Deterministic clock configuration may be used only if it is documented and exercised through the existing API. |
| The reference constitution requests Spanish documentation | The challenge instruction requiring English implementation documentation has precedence. Spanish remains only in API messages and specified business data. |
