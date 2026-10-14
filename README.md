# Manager Library Specification

> SDD technical assessment - ADSO cohort 3145555

This repository contains the source specification for Manager Library and its authoritative adaptation to the challenge stack: React and PHP with Laravel.

## Delivery Rules

- Business stories, acceptance criteria, rules, API behavior, and the data model are mandatory.
- Python and .NET references are technology examples only. They must be translated to React and Laravel.
- Documentation must be complete and coherent before product code is written.
- Only work pushed to the learner forks before the stated deadline is evaluated.
- Progress must be delivered in small, traceable commits.

## Reading Order

Read [`react-laravel-sdd/README.md`](react-laravel-sdd/README.md) first. It resolves authority and discrepancies for implementation. Then read:

1. `requirements.md` - actors, scope, stories, rules, and ambiguity register.
2. `api-contract.md` - HTTP paths, payloads, authorization, and errors.
3. `data-model.md` - the exact six-table database shape.
4. `architecture.md` - repository boundaries and React/Laravel translation.
5. `traceability.md` - evidence expected from every repository.

The `spec-python/` and `spec-.net/` folders are preserved as source evidence. They describe the same product with different backend technologies; neither backend technology is valid for this challenge.

## Repositories

| Repository | Responsibility |
|---|---|
| `test-manager-library-3145555-docs` | Source specification and authoritative React/Laravel adaptation |
| `test-manager-library-3145555-api` | Laravel API, business rules, schema migrations, and tests |
| `test-manager-library-3145555-app` | React application for internal users |
| `test-manager-library-3145555-page` | Static public presentation site |
| `test-manager-library-3145555-infra` | Containers, network, volumes, and an empty database engine |
| `test-manager-library-3145555-tool` | Utilities, including the API-only demo data seeder |

## Golden Rules

1. Do not add database entities or columns outside the authoritative data model.
2. Do not expand or reduce product scope.
3. Preserve the defined scope and repository ownership boundaries.
4. Do not write product code before the English documentation is complete and validated.
5. Keep implementation documentation in English. Spanish remains required only for user-facing API messages and fixed business data where specified.
