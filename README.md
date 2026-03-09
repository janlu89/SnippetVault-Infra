# SnippetVault Infrastructure

Docker Compose orchestration for the full SnippetVault application stack.

## 🔗 Live
- **Frontend:** https://janlu89.github.io/SnippetVault-Client/
- **API Explorer:** https://snippetvault-api.onrender.com/scalar

## 🔗 Related Repositories
- [SnippetVault-API](https://github.com/janlu89/SnippetVault-API) — .NET 10 backend
- [SnippetVault-Client](https://github.com/janlu89/SnippetVault-Client) — Angular 21 frontend

---

## Stack

| Service | Technology | Port |
|---------|-----------|------|
| Database | PostgreSQL 17 | 5432 |
| API | .NET 10 + ASP.NET Core | 8080 |
| Frontend | Angular 21 + Nginx | 80 |

---

## Running the Full Stack Locally

**Prerequisites:** Docker Desktop

```bash
git clone https://github.com/janlu89/SnippetVault-Infra.git
cd SnippetVault-Infra

# Copy the environment template and fill in your values
cp .env.example .env

# Start all three containers
docker compose up --build
```

The application will be available at `http://localhost`

The API explorer will be available at `http://localhost:8080/scalar`

On first start, EF Core automatically creates the database schema via `EnsureCreated()` — no manual migration steps needed.

---

## Environment Variables

Copy `.env.example` to `.env` and fill in the values:

| Variable | Description |
|----------|-------------|
| `DB_PASSWORD` | PostgreSQL password |
| `JWT_SECRET` | JWT signing key — minimum 32 characters |

---

## Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   client:80     │────▶│   api:8080      │────▶│   db:5432       │
│   Nginx         │     │   .NET 10       │     │   PostgreSQL 17 │
│   Angular SPA   │     │   ASP.NET Core  │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

- `client` depends on `api`
- `api` depends on `db` with a healthcheck — will not start until PostgreSQL is ready
- Named volumes persist database data and API logs between container restarts

---

## CI/CD

GitHub Actions validates `docker-compose.yml` syntax on every push to `dev` and `master`.

Production deployments are handled automatically:
- **API** — Render.com auto-deploys on every push to `master` in `SnippetVault-API`
- **Frontend** — GitHub Actions deploys to GitHub Pages on every push to `master` in `SnippetVault-Client`
