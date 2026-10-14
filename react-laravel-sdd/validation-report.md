# Specification Validation Report

## Validation Scope

Both `spec-python/` and `spec-.net/` were compared across their constitution, product specification, plan, architecture, panoramic architecture, data model, API contract, tasks, and four ADRs. Their business requirements are equivalent. Differences are limited to backend frameworks, migration tools, hashing choices, architecture test tools, and framework terminology.

## Authority Resolution

1. The challenge instructions and golden rules govern delivery language, stack, and process.
2. The source business stories and acceptance criteria govern behavior.
3. The source data model governs database shape and overrides framework conventions.
4. The source API contract governs HTTP behavior.
5. ADRs govern architecture only where they do not contradict the data model or contract.
6. Python and .NET implementation choices are non-authoritative examples.

## Preserved Product Scope

- Actors: administrator, librarian, and non-authenticating member.
- Stories: exactly HU-01 through HU-10.
- Data model: exactly `category`, `book`, `member`, `loan`, `loan_item`, and `user`.
- Fixed features: catalog, optional cover, members, loans, line returns, overdue list, and most-borrowed report.
- Explicit exclusions: fines, payments, reservations, renewals, notifications, imports, branches, publisher, publication year, language, member login, persisted reports, and audit data.

## Corrected Traps

| Finding | Resolution |
|---|---|
| Python and .NET are named throughout the source | Translate ports, adapters, migrations, tests, and runtime to Laravel; use React for the operational client. |
| Laravel commonly creates timestamp and framework columns | Product migrations use only the listed columns and no database defaults. Eloquent timestamps are disabled. |
| `user` can conflict with SQL syntax | Use the explicit table mapping and quote `library."user"` in raw PostgreSQL. |
| The cover GET route is explicit in prose but absent from one summary table | Keep `GET /api/v1/books/{id}/cover`; explicit behavior takes precedence over an incomplete summary. |
| `/health` appears beside a global `/api/v1` base path | Endpoint table paths are relative to the base; the required route is `/api/v1/health`. |
| ADR-002 requests a version witness that the model forbids | Use atomic conditional stock updates and affected-row detection. Do not add `version`. |
| T-18 calls two checks pending | Create `ck_book_total_positive` and `ck_user_role` in the initial API migration. |
| Source variants select different password algorithms | Use Laravel `Hash` behind a port; algorithm selection is a framework adapter concern. |
| The category spelling can lose its accent during adaptation | Preserve the exact UTF-8 value `Tecnología`. |
| Laravel dynamic loan routes can shadow `/loans/overdue` | Register the static route before `/loans/{id}`. |
| Source constitution asks for Spanish docs | Implementation documentation is English under the explicit challenge instruction; API messages remain Spanish. |

## Open Source Contradiction

The demo tool must create exactly three overdue loan lines through the API, but the public contract has no backdating or clock-control endpoint and new loans are due 14 days after creation. Direct SQL is forbidden. The compliant resolution is a deployment-time deterministic `Clock` adapter exercised through the existing API; it does not add an endpoint, entity, or column. This constraint must be visible in API, infra, and tool configuration and disabled in production.

## Completion Gate

Product implementation may start only after these checks pass:

- All adaptation documents are present and written in English.
- HU-01 through HU-10 appear in the requirements.
- The endpoint list and error codes match the source contract.
- Exactly six product tables and five fixed categories are listed.
- Repository ownership and all T-01 through T-23 evidence are mapped.
- Every discrepancy above has a resolution that adds no product scope.
