# Full-Stack Authentication System

![.NET 8](https://img.shields.io/badge/.NET-8.0-512BD4?logo=dotnet)
![Angular](https://img.shields.io/badge/Angular-17-DD0031?logo=angular)
![SQL Server](https://img.shields.io/badge/SQL%20Server-2022-CC2927?logo=microsoftsqlserver)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker)
![CI/CD](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=githubactions)
![License](https://img.shields.io/badge/License-MIT-green)

A production-ready full-stack authentication system demonstrating modern best practices with an **Angular 17** SPA frontend and **.NET 8 Web API** backend using **JWT Bearer** tokens, refresh token rotation, and BCrypt password hashing.

---

## Features

- **User Registration** — secure sign-up with BCrypt password hashing
- **User Login** — credential validation returning signed JWT access tokens
- **JWT Access Tokens** — short-lived (15 min) signed RS256/HS256 tokens
- **Refresh Tokens** — long-lived tokens for seamless session renewal
- **Route Guards** — Angular `CanActivate` guards protecting authenticated routes
- **HTTP Interceptors** — automatic `Authorization` header injection on all API requests
- **Swagger UI** — interactive API documentation with JWT authentication support
- **CORS** — configured for cross-origin Angular dev server requests

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     Client (Browser)                    │
│                                                         │
│  ┌──────────────────────────────────────────────────┐  │
│  │              Angular 17 SPA (:4200)               │  │
│  │                                                    │  │
│  │  ┌────────────┐  ┌──────────────┐  ┌──────────┐  │  │
│  │  │ AuthGuard  │  │  AuthService │  │ Intercep │  │  │
│  │  │ (Routes)   │  │  (JWT Store) │  │  tor     │  │  │
│  │  └────────────┘  └──────────────┘  └──────────┘  │  │
│  └──────────────────────────┬───────────────────────┘  │
└─────────────────────────────│───────────────────────────┘
                              │ HTTPS / JSON
                              ▼
┌─────────────────────────────────────────────────────────┐
│              .NET 8 Web API (:5001)                     │
│                                                         │
│  ┌──────────────────────────────────────────────────┐  │
│  │           JWT Bearer Middleware                   │  │
│  └──────────────┬───────────────────────────────────┘  │
│                 │                                        │
│  ┌──────────────▼───────────────────────────────────┐  │
│  │  AuthController  │  UsersController               │  │
│  │  POST /auth/login│  GET /users/me                 │  │
│  │  POST /auth/reg  │                                │  │
│  │  POST /auth/ref  │                                │  │
│  └──────────────┬───────────────────────────────────┘  │
│                 │                                        │
│  ┌──────────────▼───────────────────────────────────┐  │
│  │         TokenService  │  AppDbContext (EF Core)   │  │
│  └──────────────┬───────────────────────────────────┘  │
└─────────────────│───────────────────────────────────────┘
                  │ Entity Framework Core
                  ▼
┌─────────────────────────────────────────────────────────┐
│              SQL Server 2022 (:1433)                    │
│                                                         │
│  Tables: Users, RefreshTokens                          │
└─────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer       | Technology                              | Version |
|-------------|------------------------------------------|---------|
| Frontend    | Angular                                  | 17      |
| HTTP Client | Angular HttpClient + Interceptors        | 17      |
| Backend     | ASP.NET Core Web API                     | .NET 8  |
| Auth        | Microsoft.AspNetCore.Authentication.JwtBearer | 8.x |
| ORM         | Entity Framework Core + SqlServer        | 8.x     |
| Password    | BCrypt.Net-Next                          | 4.x     |
| API Docs    | Swashbuckle.AspNetCore (Swagger)         | 6.x     |
| Database    | Microsoft SQL Server                     | 2022    |
| Containers  | Docker + Docker Compose                  | latest  |
| CI/CD       | GitHub Actions                           | —       |

---

## Getting Started

### Option 1: Docker Compose (Recommended)

**Prerequisites:** Docker Desktop installed and running.

```bash
git clone https://github.com/miteshdekate93/TestAuthenticationUser1.git
cd TestAuthenticationUser1

docker compose up --build
```

Services will be available at:
- Angular frontend: http://localhost:4200
- .NET 8 API + Swagger: http://localhost:5001/swagger
- SQL Server: localhost:1433

### Option 2: Manual Setup

**Prerequisites:** .NET 8 SDK, Node.js 18+, SQL Server 2022 (or LocalDB)

#### 1. Start the API

```bash
cd api-v2

# Update connection string in appsettings.json
# "DefaultConnection": "Server=localhost;Database=AuthDb;Trusted_Connection=True;"

dotnet restore
dotnet ef database update
dotnet run
# API available at https://localhost:5001
```

#### 2. Start the Angular App

```bash
cd Angular
npm ci
npx ng serve
# App available at http://localhost:4200
```

---

## API Endpoints

| Method | Endpoint            | Auth Required | Description                          |
|--------|---------------------|---------------|--------------------------------------|
| POST   | `/auth/register`    | No            | Register a new user account          |
| POST   | `/auth/login`       | No            | Authenticate and receive JWT tokens  |
| POST   | `/auth/refresh`     | No            | Exchange refresh token for new JWT   |
| GET    | `/users/me`         | Yes (Bearer)  | Get the current authenticated user   |

### Request / Response Examples

**POST /auth/register**
```json
// Request
{ "username": "alice", "email": "alice@example.com", "password": "P@ssw0rd!" }

// Response 201 Created
{ "message": "User registered successfully." }
```

**POST /auth/login**
```json
// Request
{ "email": "alice@example.com", "password": "P@ssw0rd!" }

// Response 200 OK
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "dGhpcyBpcyBhIHJlZnJlc2g...",
  "expiresIn": 900
}
```

**POST /auth/refresh**
```json
// Request
{ "refreshToken": "dGhpcyBpcyBhIHJlZnJlc2g..." }

// Response 200 OK
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "bmV3UmVmcmVzaFRva2Vu...",
  "expiresIn": 900
}
```

---

## Security Features

- **BCrypt Password Hashing** — passwords are never stored in plain text; BCrypt with a work factor of 12 is used
- **Short-lived JWT Access Tokens** — tokens expire in 15 minutes to limit exposure from token theft
- **Refresh Token Rotation** — each refresh issues a new refresh token and invalidates the old one
- **Refresh Token Revocation** — tokens are stored server-side and can be individually revoked
- **HTTPS Enforcement** — API redirects HTTP to HTTPS in production
- **CORS Policy** — only configured origins may call the API
- **Input Validation** — `[Required]`, `[EmailAddress]`, `[MinLength]` Data Annotations on all request models
- **Parameterized Queries** — EF Core prevents SQL injection by default

---

## Project Structure

```
TestAuthenticationUser1/
├── Angular/                  # Angular 17 SPA
│   └── src/app/
│       ├── auth/             # AuthGuard + Interceptor
│       ├── user/             # Sign-in / Sign-up components
│       └── home/             # Protected home route
├── api-v2/                   # .NET 8 Web API (new)
│   ├── Controllers/
│   ├── Models/
│   ├── Services/
│   ├── Data/
│   ├── Program.cs
│   └── Dockerfile
├── WebAPI/                   # Legacy ASP.NET OWIN API (reference)
├── docker-compose.yml
└── .github/workflows/ci-cd.yml
```

---

## License

MIT — see [LICENSE](LICENSE) for details.
