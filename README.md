# 🚀 Xcrap Private Server — Backend

> Backend robusto em **NestJS 11** para orquestração de web scrapers, extração declarativa de dados e gerenciamento de clientes HTTP utilizando o ecossistema `@xcrap`.

[![NestJS](https://img.shields.io/badge/NestJS-11.x-E0234E?logo=nestjs&logoColor=white)](https://nestjs.com/)
[![Prisma](https://img.shields.io/badge/Prisma-6.x-2D3748?logo=prisma&logoColor=white)](https://www.prisma.io/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![SQLite](https://img.shields.io/badge/SQLite-Database-003B57?logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![Swagger](https://img.shields.io/badge/OpenAPI-Swagger-85EA2D?logo=swagger&logoColor=black)](http://localhost:3003/docs)

---

## 📌 Sobre o Projeto

O **Xcrap Private Server Backend** é o núcleo de serviços da plataforma Xcrap Cloud. Ele centraliza a configuração, armazenamento e execução de pipelines de scraping através de diferentes clientes HTTP, além de oferecer controle de acesso seguro para usuários, administradores e SDKs externos.

### Principais Funcionalidades:
- 🔐 **Multi-Autenticação**: Suporte híbrido via **JWT (Bearer Token)** para sessões no frontend e **API Key (`x-api-key`)** para chamadas via SDK ou automações externas.
- 🕷️ **Execução de Scrapers Dinâmicos e Persistidos**: Suporte à execução de scrapers salvos ou execução imediata sob demanda (`/scrapers/dynamic/execute`) com modelos de parsing estruturados (HTML, JSON, Markdown).
- 🌐 **Múltiplos Clientes HTTP Especializados**: Suporte nativo a diferentes motores de requisição (`http_core`, `axios`, `got_scraping` para evasão avançada, e `impit`).
- 🛡️ **Segurança e Criptografia**: API Keys de usuários criptografadas com **AES-256-CBC**, senhas com **bcrypt**, headers de segurança com **Helmet** e validação global estrita de payloads com **class-validator**.
- 📄 **Paginação e Busca Otimizada**: Paginação padronizada em todos os módulos (`/users`, `/clients`, `/scrapers`) com busca textual por substring (`contains`) e insensível a maiúsculas/minúsculas.
- 📑 **Swagger / OpenAPI Integrado**: Documentação viva e gerador automático de especificação em `swagger-spec.json`.

---

## 🛠️ Tecnologias Utilizadas

- **Framework**: [NestJS](https://nestjs.com/) (Express, Config, Passport, JWT, Swagger)
- **ORM / Banco de Dados**: [Prisma](https://www.prisma.io/) com SQLite
- **Módulos `@xcrap`**:
  - `@xcrap/core` — Estruturas fundamentais de scraping e requisição HTTP
  - `@xcrap/factory` — Fábrica de criação de clientes e modelos de parsing
  - `@xcrap/parser` — Motores de extração estruturada (HTML, JSON, Markdown)
  - `@xcrap/axios-client` — Cliente HTTP baseado em Axios
  - `@xcrap/got-scraping-client` — Cliente com evasão de bloqueios e fingerprinting
  - `@xcrap/impit-client` — Cliente HTTP de alta performance
- **Paginação**: `prisma-pagination`
- **Validação / Sanitização**: `class-validator`, `class-transformer`

---

## 📂 Estrutura de Pastas

```bash
src/
├── auth/            # Módulo de autenticação (JWT, Local, API Key, Guards e Decorators)
├── clients/         # Módulo de gerenciamento de clientes HTTP configuráveis
├── scrapers/        # Módulo de orquestração e execução de scrapers
├── users/           # Gerenciamento de usuários e permissões (RBAC)
├── crypto/          # Serviço de criptografia simétrica (AES-256-CBC) e hash (bcrypt)
├── parser/          # Tipagens e utilitários dos modelos de parsing
├── helpers/         # Configurações globais, extractors permitidos e constantes
├── common/          # Guards, pipes de validação (Min/Max number) e filters
└── main.ts          # Ponto de entrada da aplicação NestJS
prisma/
├── schema.prisma    # Modelagem do banco de dados (User, Client, Scraper)
├── seed.ts          # Criação do usuário administrador e clientes padrão
└── dev.db           # Banco SQLite de desenvolvimento
```

---

## ⚙️ Variáveis de Ambiente (`.env`)

Crie um arquivo `.env` na raiz do projeto baseado no `.env.example`:

```bash
cp .env.example .env
```

| Variável | Descrição | Exemplo |
| :--- | :--- | :--- |
| `PORT` | Porta onde o servidor responderá | `3003` |
| `DATABASE_URL` | URL de conexão do Prisma SQLite | `"file:./dev.db"` |
| `JWT_PRIVATE_KEY` | Chave secreta para assinatura dos tokens JWT | `[string hex]` |
| `ACCESS_SIGN_EXPIRES_IN` | Tempo de expiração do Access Token | `7d` |
| `REFRESH_SIGN_EXPIRES_IN` | Tempo de expiração do Refresh Token | `30d` |
| `BCRYPT_SALT_ROUNDS` | Rounds de salt para o hash de senhas | `12` |
| `ENCRYPTION_ALGORITHM` | Algoritmo simétrico para dados sensíveis | `aes-256-cbc` |
| `ENCRYPTION_KEY` | Chave de criptografia de 256 bits (32 bytes em hex) | `[64 caracteres hex]` |
| `ENCRYPTION_IV` | Vetor de inicialização (16 bytes em hex) | `[32 caracteres hex]` |
| `ADMIN_DEFAULT_ID` | UUID fixo para o administrador no seed | `[uuid]` |
| `ADMIN_DEFAULT_USERNAME`| Username do administrador inicial | `admin` |
| `ADMIN_DEFAULT_EMAIL` | E-mail do administrador inicial | `admin@xcrap.dev` |
| `ADMIN_DEFAULT_API_KEY` | API Key inicial do administrador | `adm_xxxxxxxx...` |
| `ADMIN_DEFAULT_NAME` | Nome do administrador | `Administrador` |
| `ADMIN_DEFAULT_PASSWORD`| Senha do administrador | `senha_segura` |

---

## 🚀 Instalação e Execução

### 1. Instalar as Dependências

```bash
npm install
```

### 2. Configurar o Banco de Dados (Prisma)

Gere o cliente do Prisma, aplique as migrações e popule o banco com o seed inicial:

```bash
# Gerar o cliente Prisma
npx prisma generate

# Aplicar migrações
npx prisma migrate dev

# Executar o seed (cria admin e clientes padrão)
npx prisma db seed
```

> **Dica**: Para inspecionar visualmente os dados do banco SQLite:
> ```bash
> npx prisma studio
> ```

### 3. Iniciar o Servidor

```bash
# Modo de desenvolvimento com hot-reload
npm run start:dev

# Modo de produção
npm run build
npm run start:prod
```

O servidor estará disponível por padrão em: **`http://localhost:3003`**

---

## 📖 Documentação da API (Swagger)

Com o servidor rodando em modo de desenvolvimento, acesse a documentação interativa:

- **Swagger UI**: [http://localhost:3003/docs](http://localhost:3003/docs)
- **Atalho no Windows**:
  ```bash
  npm run open-docs
  ```

---

## 🛣️ Rotas Principais (Visão Geral)

### 🔐 Autenticação (`/auth`)
- `POST /auth/sign-in` — Autenticação de usuário (gera Access & Refresh Token)
- `POST /auth/sign-up` — Cadastro de novo usuário
- `POST /auth/refresh-token` — Renovação do Access Token

### 👤 Usuários (`/users`) *(Requer Admin)*
- `GET /users?page=1&perPage=20&name=...` — Listagem paginada com filtro de nome
- `POST /users` — Criação manual de usuário
- `GET /users/:id` — Detalhes do usuário
- `PATCH /users/:id` — Atualização de usuário
- `DELETE /users/:id` — Remoção de usuário

### 🌐 Clientes HTTP (`/clients`) *(Requer Autenticação)*
- `GET /clients?page=1&perPage=20&name=...` — Listagem paginada de clientes
- `POST /clients` — Cadastro de cliente HTTP personalizado
- `GET /clients/:id` — Detalhes do cliente
- `PATCH /clients/:id` — Atualização de configurações
- `DELETE /clients/:id` — Exclusão de cliente

### 🕷️ Scrapers (`/scrapers`) *(Requer Autenticação)*
- `GET /scrapers?page=1&perPage=20&name=...` — Listagem paginada de scrapers
- `POST /scrapers` — Cadastro de scraper com modelo de parsing
- `GET /scrapers/:id` — Detalhes do scraper
- `POST /scrapers/:id/execute` — Execução de um scraper cadastrado
- `POST /scrapers/dynamic/execute` — Execução direta de um scraper dinâmico sob demanda
- `PATCH /scrapers/:id` — Atualização do scraper
- `DELETE /scrapers/:id` — Remoção do scraper

---

## 🧪 Testes e Qualidade de Código

```bash
# Executar testes unitários
npm run test

# Executar testes end-to-end (e2e)
npm run test:e2e

# Verificar cobertura de testes
npm run test:cov

# Linter e Formatação
npm run lint
npm run format
```

---

## 👨‍💻 Autor

Desenvolvido por **Marcuth** como parte do ecossistema [Xcrap Cloud](https://github.com/Xcrap-Cloud).
