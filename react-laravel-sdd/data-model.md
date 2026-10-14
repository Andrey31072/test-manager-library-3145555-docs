# Data Model Checklist

Engine: PostgreSQL 16.
Schema: `library`.
The database starts empty in infra. Laravel migrations create all DDL, constraints, indexes, and fixed category seed.

## Tables

Exactly six tables are allowed.

| Table | Columns |
|---|---|
| `category` | `id uuid PK`, `name varchar(120) not null unique` |
| `book` | `id uuid PK`, `title varchar(250) not null`, `author varchar(200) not null`, `isbn char(13) not null`, `category_id uuid not null`, `total_copies integer not null`, `available_copies integer not null`, `cover_key varchar(512) null`, `deleted_at timestamptz null` |
| `member` | `id uuid PK`, `full_name varchar(200) not null`, `document_number varchar(30) not null unique` |
| `loan` | `id uuid PK`, `member_id uuid not null`, `loaned_by_user_id uuid not null`, `loaned_at timestamptz not null` |
| `loan_item` | `id uuid PK`, `loan_id uuid not null`, `book_id uuid not null`, `book_title varchar(250) not null`, `isbn char(13) not null`, `category_name varchar(120) not null`, `due_at timestamptz not null`, `returned_at timestamptz null` |
| `user` | `id uuid PK`, `username varchar(120) not null unique`, `password_hash varchar(512) not null`, `role varchar(40) not null` |

No table has `created_at`, `updated_at`, report status, days late, fines, payments, reservations, or audit columns.
No column has a database default.

## Required Constraints and Indexes

- Primary keys for all six tables.
- Unique `category.name`.
- Unique `member.document_number`.
- Unique `user.username`.
- Partial unique index for `book.isbn` where `deleted_at is null`.
- Foreign keys:
  - `book.category_id` to `category.id` restrict.
  - `loan.member_id` to `member.id` restrict.
  - `loan.loaned_by_user_id` to `user.id` restrict.
  - `loan_item.loan_id` to `loan.id` cascade.
  - `loan_item.book_id` to `book.id` restrict.
- `ck_book_available_range`: `available_copies >= 0 and available_copies <= total_copies`.
- `ck_book_total_positive`: `total_copies >= 1`.
- `ck_user_role`: `role in ('admin', 'librarian')`.
- Unique `(loan_id, book_id)`.
- Access indexes:
  - partial `book(category_id, title)` over active books.
  - `loan(loaned_at)`.
  - `loan_item(book_id)`.
  - partial `loan_item(due_at)` where `returned_at is null`.

## Fixed Categories

| id | name |
|---|---|
| `11111111-1111-4111-8111-111111111111` | Novela |
| `22222222-2222-4222-8222-222222222222` | Ciencia |
| `33333333-3333-4333-8333-333333333333` | Tecnologia |
| `44444444-4444-4444-8444-444444444444` | Historia |
| `55555555-5555-4555-8555-555555555555` | Infantil |

## Verification Queries

The implementation must keep the reference verification approach:

```sql
SELECT table_name, ordinal_position, column_name, data_type, is_nullable, column_default
FROM information_schema.columns
WHERE table_schema = 'library'
ORDER BY 1, 2;

SELECT c.conrelid::regclass, c.conname, c.contype, pg_get_constraintdef(c.oid)
FROM pg_constraint c
JOIN pg_namespace n ON n.oid = c.connamespace
WHERE n.nspname = 'library'
ORDER BY 1, 3, 2;

SELECT tablename, indexname, indexdef
FROM pg_indexes
WHERE schemaname = 'library'
ORDER BY 1, 2;
```
