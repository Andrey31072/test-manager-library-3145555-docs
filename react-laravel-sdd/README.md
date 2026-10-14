# Manager Library SDD Adaptation for React and Laravel

This folder is the English, implementation-facing adaptation of the reference specifications in `spec-python/` and `spec-.net/`.
It must be read before changing code in the five implementation repositories.

## Authority Order

1. Business requirements, user stories, acceptance criteria, API contract, and data model from the reference spec are mandatory.
2. `data-model.md` is authoritative for database shape. If code or other docs disagree with the database, the document or code must be corrected.
3. `api-contract.md` is authoritative for HTTP paths, methods, payload shape, error body, status codes, roles, pagination, and date formats.
4. Python and .NET framework choices are reference-only and are translated to Laravel, React, Docker, and Node utilities.
5. Repository README files confirm that the five implementation repositories intentionally start empty.

## Included Documents

- `requirements.md`: product scope, actors, stories, business rules, non-functional requirements, and trap register.
- `architecture.md`: React/Laravel adaptation of the hexagonal architecture and repository responsibilities.
- `api-contract.md`: implementation checklist for the required HTTP contract.
- `data-model.md`: PostgreSQL schema checklist and migration rules.
- `traceability.md`: task-to-repository evidence matrix used to keep commits small and aligned.
- `validation-report.md`: source comparison, authority decisions, corrected traps, and the implementation gate.

## Non-Negotiable Scope Boundaries

Do not add database entities beyond the six specified tables: `category`, `book`, `member`, `loan`, `loan_item`, and `user`.
Do not add fines, payments, reservations, renewals, email/SMS, imports, branches, publishers, years, languages, audit columns, report tables, or member login.
Derived values such as loan status and days late are calculated, not stored.

## Stack Translation

| Reference concern | Reference choices | Challenge implementation |
|---|---|---|
| Backend runtime | Python/FastAPI or .NET/Minimal API | PHP 8.3 with Laravel |
| Persistence migrations | Alembic or EF migrations | Laravel migrations, including schema `library` and fixed category seed |
| Architecture tests | import-linter or NetArchTest | PHPUnit architecture-style tests over namespaces and dependency boundaries |
| Password hashing | Argon2id or BCrypt | Laravel `Hash` service behind an application port |
| Token issuing | JWT HS256 | Laravel service issuing JWT-compatible bearer tokens with `sub`, `role`, and 8-hour expiry |
| Frontend | App role only described | React app consuming `/api/v1` |
| Demo seed | Tool calls API | Node CLI seeder using the public API only |

## Source Preservation and Language

The two source folders are retained as audit evidence. This English adaptation is authoritative for implementation because it reconciles their technology-specific differences with the required React/Laravel stack. Spanish is used only where the business contract requires it: user-facing API messages and fixed business values.

Repository branches and files must be inspected at implementation time. Their state is not frozen in this specification.
