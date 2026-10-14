# 🚀 Xcrap Private Server — Backend

> Robust **NestJS 11** backend for web scraping orchestration, declarative data parsing, and HTTP client management powered by the `@xcrap` ecosystem.

[![NestJS](https://img.shields.io/badge/NestJS-11.x-E0234E?logo=nestjs&logoColor=white)](https://nestjs.com/)
[![Prisma](https://img.shields.io/badge/Prisma-6.x-2D3748?logo=prisma&logoColor=white)](https://www.prisma.io/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![SQLite](https://img.shields.io/badge/SQLite-Database-003B57?logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![Swagger](https://img.shields.io/badge/OpenAPI-Swagger-85EA2D?logo=swagger&logoColor=black)](http://localhost:3003/docs)

---

## 📌 About the Project

The **Xcrap Private Server Backend** is the core service hub for the Xcrap Cloud platform. It centralizes configuration, storage, and execution of web scraping pipelines across multiple HTTP clients while providing secure access control for web dashboards, administrators, and external SDKs.

### Key Features:
- 🔐 **Multi-Authentication Support**: Hybrid authentication via **JWT (Bearer Token)** for user dashboard sessions and **API Key (`x-api-key`)** for automated SDKs and external integrations.
- 🕷️ **Dynamic & Persisted Scraper Execution**: Execute pre-configured database scrapers or run on-demand ad-hoc scrapers (`/scrapers/dynamic/execute`) with declarative parsing models (HTML, JSON, Markdown).
- 🌐 **Specialized HTTP Clients**: Native integration with multiple request engines (`http_core`, `axios`, `got_scraping` for advanced anti-bot evasion, and `impit`).
- 🛡️ **Security & Encryption**: Sensitive user API Keys encrypted at rest using **AES-256-CBC**, password hashing with **bcrypt**, secure headers via **Helmet**, and strict DTO validation with **class-validator**.
- 📄 **Standardized Pagination & Search**: Uniform pagination across all modules (`/users`, `/clients`, `/scrapers`) featuring case-insensitive substring searching (`contains`).
- 📑 **Interactive Swagger / OpenAPI**: Embedded documentation UI and automatic generation of `swagger-spec.json`.

---

## 🛠️ Tech Stack

- **Framework**: [NestJS](https://nestjs.com/) 11 (Express, Config, Passport, JWT, Swagger)
- **Database / ORM**: [Prisma](https://www.prisma.io/) with SQLite
- **`@xcrap` Ecosystem**:
  - `@xcrap/core` — Core scraping and HTTP client abstractions
  - `@xcrap/factory` — Client and parsing model creation factories
  - `@xcrap/parser` — Structured data extraction engines (HTML, JSON, Markdown)
  - `@xcrap/axios-client` — Axios-based HTTP client
  - `@xcrap/got-scraping-client` — Fingerprint emulation and anti-bot evasion client
  - `@xcrap/impit-client` — High-performance HTTP client
- **Pagination**: `prisma-pagination`
- **Validation**: `class-validator`, `class-transformer`

---

## 📂 Project Structure

```bash
src/
├── auth/            # Authentication module (JWT, Local, API Key, Guards, Decorators)
├── clients/         # Configurable HTTP clients management
├── scrapers/        # Scraper orchestration, execution and parsing pipeline
├── users/           # User management and Role-Based Access Control (RBAC)
├── crypto/          # Symmetric encryption service (AES-256-CBC) & hashing (bcrypt)
├── parser/          # Parsing model typings and validation utilities
├── helpers/         # Global configuration helpers, allowed extractors & constants
├── common/          # Reusable guards, validation pipes (Min/Max number), filters
└── main.ts          # Application entry point
prisma/
├── schema.prisma    # Database schema (User, Client, Scraper models)
├── seed.ts          # Seed script for initial admin user and default clients
└── dev.db           # SQLite database file
```

---

## ⚙️ Environment Variables (`.env`)

Create a `.env` file in the project root based on `.env.example`:

```bash
cp .env.example .env
```

| Variable | Description | Example |
| :--- | :--- | :--- |
| `PORT` | HTTP server port | `3003` |
| `DATABASE_URL` | Prisma SQLite connection URL | `"file:./dev.db"` |
| `JWT_PRIVATE_KEY` | Secret key for signing JWT tokens | `[hex string]` |
| `ACCESS_SIGN_EXPIRES_IN` | Access token lifespan | `7d` |
| `REFRESH_SIGN_EXPIRES_IN` | Refresh token lifespan | `30d` |
| `BCRYPT_SALT_ROUNDS` | Salt rounds for password hashing | `12` |
| `ENCRYPTION_ALGORITHM` | Symmetric encryption algorithm | `aes-256-cbc` |
| `ENCRYPTION_KEY` | 256-bit encryption key (32 bytes in hex) | `[64 hex chars]` |
| `ENCRYPTION_IV` | Initialization vector (16 bytes in hex) | `[32 hex chars]` |
| `ADMIN_DEFAULT_ID` | Fixed UUID for the seeded administrator | `[uuid]` |
| `ADMIN_DEFAULT_USERNAME`| Default admin username | `admin` |
| `ADMIN_DEFAULT_EMAIL` | Default admin email | `admin@xcrap.dev` |
| `ADMIN_DEFAULT_API_KEY` | Default admin API key | `adm_xxxxxxxx...` |
| `ADMIN_DEFAULT_NAME` | Default admin display name | `Admin` |
| `ADMIN_DEFAULT_PASSWORD`| Default admin password | `secure_password` |

---

## 🚀 Getting Started

### 1. Install Dependencies

```bash
npm install
```

### 2. Setup the Database (Prisma)

Generate the Prisma Client, apply database migrations, and run the initial seed:

```bash
# Generate Prisma Client
npx prisma generate

# Apply migrations
npx prisma migrate dev

# Run the seed script (creates admin user and default HTTP clients)
npx prisma db seed
```

> **Tip**: You can visually inspect and manage SQLite data using Prisma Studio:
> ```bash
> npx prisma studio
> ```

### 3. Run the Application

```bash
# Development mode with hot-reload
npm run start:dev

# Production build & run
npm run build
npm run start:prod
```

By default, the server runs at: **`http://localhost:3003`**

---

## 📖 API Documentation (Swagger)

When running in development mode, access the interactive Swagger UI:

- **Swagger UI**: [http://localhost:3003/docs](http://localhost:3003/docs)
- **Windows Shortcut**:
  ```bash
  npm run open-docs
  ```

---

## 🛣️ API Endpoints Overview

### 🔐 Authentication (`/auth`)
- `POST /auth/sign-in` — Authenticate user (returns Access & Refresh Token)
- `POST /auth/sign-up` — Register a new user
- `POST /auth/refresh-token` — Renew an expired Access Token

### 👤 Users (`/users`) *(Admin Only)*
- `GET /users?page=1&perPage=20&name=...` — Paginated user listing with search filter
- `POST /users` — Create user manually
- `GET /users/:id` — Get user details
- `PATCH /users/:id` — Update user
- `DELETE /users/:id` — Remove user

### 🌐 HTTP Clients (`/clients`) *(Authenticated)*
- `GET /clients?page=1&perPage=20&name=...` — Paginated clients listing with search filter
- `POST /clients` — Register a new custom HTTP client configuration
- `GET /clients/:id` — Get client details
- `PATCH /clients/:id` — Update client configuration
- `DELETE /clients/:id` — Delete client

### 🕷️ Scrapers (`/scrapers`) *(Authenticated)*
- `GET /scrapers?page=1&perPage=20&name=...` — Paginated scrapers listing with search filter
- `POST /scrapers` — Create a scraper with declarative parsing model
- `GET /scrapers/:id` — Get scraper details
- `POST /scrapers/:id/execute` — Execute a saved scraper by ID
- `POST /scrapers/dynamic/execute` — Execute an ad-hoc dynamic scraper payload
- `PATCH /scrapers/:id` — Update scraper
- `DELETE /scrapers/:id` — Delete scraper

---

## 🧪 Testing & Code Quality

```bash
# Run unit tests
npm run test

# Run end-to-end (e2e) tests
npm run test:e2e

# Run test coverage
npm run test:cov

# Linter and Code Formatting
npm run lint
npm run format
```

---

## 👨‍💻 Author

Maintained by **Marcuth** as part of the [Xcrap Cloud](https://github.com/Xcrap-Cloud) ecosystem.
