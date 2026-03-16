# Auth Fullstack App

![CI](https://github.com/miteshdekate93/TestAuthenticationUser1/actions/workflows/ci-cd.yml/badge.svg)
![Angular](https://img.shields.io/badge/Angular-17-red)
![.NET 8](https://img.shields.io/badge/.NET-8-purple)
![Docker](https://img.shields.io/badge/Docker-ready-2496ED)

A full-stack authentication demo — Angular 17 frontend with a .NET 8 API backend using JWT tokens. Shows register, login, protected routes, and refresh tokens. Originally built with OWIN OAuth, upgraded to modern JWT Bearer auth.

## What It Does

- Register and login with email + password (BCrypt hashed)
- JWT access tokens (short-lived) + refresh tokens (long-lived)
- Angular route guards protect pages that require login
- HTTP interceptor auto-attaches the JWT header to API calls
- Refresh token flow keeps users logged in without re-entering password

## Tech Stack

| Part | Technology |
|------|-----------|
| Frontend | Angular 17 (route guards, HTTP interceptors) |
| Backend | .NET 8 Web API |
| Auth | JWT Bearer + Refresh Tokens |
| Security | BCrypt password hashing |
| Database | SQL Server + Entity Framework Core |
| Container | Docker Compose |
| CI | GitHub Actions |

## Run It

```bash
git clone https://github.com/miteshdekate93/TestAuthenticationUser1.git
cd TestAuthenticationUser1
docker-compose up --build
```
- Angular app: http://localhost:4200
- API + Swagger: http://localhost:5001/swagger

## Auth Flow

```
POST /auth/register   → create account, get tokens
POST /auth/login      → validate credentials, get tokens
GET  /users/me        → protected — requires JWT in header
POST /auth/refresh    → swap refresh token for new access token
```

## Project Structure

```
TestAuthenticationUser1/
├── Angular/      Angular 17 SPA (guards, interceptors, auth service)
├── api-v2/       .NET 8 Web API with JWT auth (new)
├── WebAPI/       Original OWIN/OAuth API (legacy reference)
└── docker-compose.yml
```
