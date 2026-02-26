# Tech Stack Presets

Reference presets for popular frameworks. Use these as starting suggestions, then customize based on user needs.

---

## Python / FastAPI

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | SQLModel | Pydantic + SQLAlchemy integration, type-safe |
| **Migrations** | Alembic | Standard for SQLAlchemy-based ORMs |
| **Validation** | Pydantic v2 | Native FastAPI integration, fast |
| **Testing** | pytest + pytest-asyncio | Async support, fixtures |
| **HTTP Client** | httpx | Async support for testing |
| **Database (dev)** | SQLite | Zero setup, portable |
| **Database (prod)** | PostgreSQL | Robust, scalable |
| **Auth** | python-jose (JWT) | Lightweight, standard |
| **API Docs** | OpenAPI (auto) | Built into FastAPI |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Python 3.11+, FastAPI with async/await
- SQLModel ORM (NOT raw SQL), Alembic migrations
- Pydantic v2 for validation
- pytest + pytest-asyncio for testing
- SQLite (dev) → PostgreSQL (prod)
```

---

## Python / Django

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | Django ORM | Built-in, mature, well-documented |
| **Migrations** | Django migrations | Built-in, automatic |
| **Validation** | Django forms / DRF serializers | Native integration |
| **Testing** | pytest-django | Better than unittest, fixtures |
| **API** | Django REST Framework | Standard for Django APIs |
| **Database (dev)** | SQLite | Default, zero setup |
| **Database (prod)** | PostgreSQL | Django's preferred DB |
| **Auth** | Django auth + DRF tokens | Built-in, extensible |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Python 3.11+, Django 5.x
- Django ORM (built-in migrations)
- Django REST Framework for API
- pytest-django for testing
- SQLite (dev) → PostgreSQL (prod)
```

---

## Node.js / Express

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | Prisma | Type-safe, great DX, auto migrations |
| **Alternative ORM** | TypeORM | More traditional, decorators |
| **Validation** | Zod | TypeScript-first, composable |
| **Testing** | Jest + Supertest | Standard, good async support |
| **Database (dev)** | SQLite | Prisma supports it well |
| **Database (prod)** | PostgreSQL | Robust, Prisma optimized |
| **Auth** | Passport.js + JWT | Flexible, many strategies |
| **TypeScript** | Strict mode | Catch errors early |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Node.js 20+, Express with TypeScript (strict)
- Prisma ORM with auto migrations
- Zod for validation
- Jest + Supertest for testing
- SQLite (dev) → PostgreSQL (prod)
```

---

## Node.js / NestJS

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | Prisma or TypeORM | Both well-supported |
| **Validation** | class-validator | Native NestJS integration |
| **Testing** | Jest (built-in) | Comes with NestJS |
| **Database** | PostgreSQL | Best support |
| **Auth** | @nestjs/passport + JWT | Official module |
| **Structure** | Modules | Built-in, enforced |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Node.js 20+, NestJS with TypeScript
- Prisma ORM, auto migrations
- class-validator + class-transformer
- Jest for testing (built-in)
- PostgreSQL
```

---

## Go

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | sqlc (queries) | Type-safe SQL, no runtime overhead |
| **Alternative** | GORM | Full ORM if needed |
| **Migrations** | golang-migrate | Standard, flexible |
| **Router** | chi or gin | chi is stdlib-compatible, gin is fast |
| **Validation** | go-playground/validator | Tags-based, standard |
| **Testing** | go test + testify | Built-in + assertions |
| **Database** | PostgreSQL | Best Go driver support |
| **Auth** | golang-jwt | Standard JWT library |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Go 1.22+
- sqlc for type-safe queries (NOT raw SQL)
- golang-migrate for migrations
- chi router (stdlib compatible)
- go test + testify for testing
- PostgreSQL
```

---

## Java / Spring Boot

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | Spring Data JPA / Hibernate | Standard, mature |
| **Migrations** | Flyway | SQL-based, versioned |
| **Alternative** | Liquibase | XML/YAML if preferred |
| **Validation** | Jakarta Validation | Built-in, annotations |
| **Testing** | JUnit 5 + Mockito | Standard stack |
| **Database (dev)** | H2 | In-memory, fast tests |
| **Database (prod)** | PostgreSQL | Enterprise standard |
| **Auth** | Spring Security + JWT | Comprehensive, flexible |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Java 21+, Spring Boot 3.x
- Spring Data JPA / Hibernate
- Flyway for migrations
- JUnit 5 + Mockito for testing
- H2 (dev/test) → PostgreSQL (prod)
```

---

## Rust / Axum

| Category | Recommendation | Rationale |
|----------|----------------|-----------|
| **ORM** | SQLx (compile-time checked) | Type-safe, async |
| **Alternative** | Diesel | Sync, mature |
| **Migrations** | SQLx migrations | Built-in |
| **Validation** | validator crate | Derive macros |
| **Testing** | cargo test + tower-test | Built-in + HTTP testing |
| **Database** | PostgreSQL | Best async support |
| **Auth** | axum-extra + jsonwebtoken | JWT with extractors |

**Example .claude/rules snippet:**
```markdown
## Tech Stack
- Rust 1.75+, Axum framework
- SQLx with compile-time query checking
- SQLx migrations
- cargo test + tower-test
- PostgreSQL
```
