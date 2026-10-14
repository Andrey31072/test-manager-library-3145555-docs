# API Contract Checklist

Base path: `/api/v1`.
Payloads are JSON UTF-8 with `snake_case` fields.
IDs are UUID v4 strings.
Instants use ISO 8601 UTC with `Z`.
Pagination is `page` and `size`, where `size <= 50`, returning `{ "items": [], "page": 1, "size": 20, "total": 0 }`.

## Unified Error Body

```json
{
  "code": "VALIDATION_ERROR",
  "message": "El campo enviado no es valido.",
  "details": [
    { "field": "items[0].book_id", "issue": "sin ejemplares" }
  ]
}
```

`details` may be empty and must never include traces, SQL, server paths, passwords, or password hashes.

## Business Error Codes

| Code | HTTP status |
|---|---|
| `INVALID_CREDENTIALS` | 401 |
| `FORBIDDEN_ROLE` | 403 |
| `NOT_FOUND` | 404 |
| `USERNAME_TAKEN` | 409 |
| `ISBN_TAKEN` | 409 |
| `DOCUMENT_TAKEN` | 409 |
| `BOOK_HAS_OPEN_LOANS` | 409 |
| `BOOK_NOT_AVAILABLE` | 409 |
| `MEMBER_HAS_OVERDUE` | 409 |
| `ITEM_ALREADY_RETURNED` | 409 |
| `MEMBER_LIMIT_EXCEEDED` | 422 |
| `DUPLICATE_BOOK_IN_LOAN` | 422 |
| `VALIDATION_ERROR` | 422 |
| `INVALID_DATE_RANGE` | 422 |

## Endpoints

| Method | Path | Role |
|---|---|---|
| `POST` | `/auth/login` | public |
| `GET` | `/health` | public |
| `POST` | `/users` | admin |
| `GET` | `/categories` | authenticated |
| `GET` | `/books` | authenticated |
| `GET` | `/books/{id}` | authenticated |
| `GET` | `/books/{id}/cover` | authenticated |
| `POST` | `/books` | admin |
| `PUT` | `/books/{id}` | admin |
| `DELETE` | `/books/{id}` | admin |
| `PUT` | `/books/{id}/cover` | admin |
| `DELETE` | `/books/{id}/cover` | admin |
| `GET` | `/members` | admin, librarian |
| `GET` | `/members/{id}` | admin, librarian |
| `POST` | `/members` | admin, librarian |
| `POST` | `/loans` | admin, librarian |
| `GET` | `/loans` | admin, librarian |
| `GET` | `/loans/{id}` | admin, librarian |
| `POST` | `/loans/{loan_id}/items/{item_id}/return` | admin, librarian |
| `GET` | `/loans/overdue` | admin, librarian |
| `GET` | `/reports/most-borrowed` | admin |

## Response Rules

- `POST /auth/login` returns `access_token`, `token_type`, `expires_in`, and `role`.
- User responses never include `password` or `password_hash`.
- Book responses include `category` object and `cover_key`, where no cover is `null`.
- Loan status is calculated as `open`, `overdue`, or `closed`.
- Overdue responses include `loan_id`, `item_id`, `book_title`, `member_name`, `due_at`, and `days_late`.
- The report returns `from`, `to`, and `rows`; rows include frozen book title and category name and exclude members and librarians.
