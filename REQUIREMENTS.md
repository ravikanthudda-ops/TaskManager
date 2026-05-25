# TaskManager — TaskManager Web API Requirements

> This is Track B of the 8-week interview readiness plan.
> Build this API week by week alongside the daily CSharpWarmup exercises.
> Every concept learned in Track A (caching, async, background tasks, testing) gets applied here.

---

## Project Vision

Build a **Task Management REST API** — a simplified version of what you'd find in tools like Jira or Trello.
It manages users, tasks, and assignments with authentication, filtering, pagination, and background processing.

This project exists to demonstrate:
- Clean Architecture understanding
- Real-world API design decisions
- Security implementation (JWT)
- Performance thinking (caching, pagination, indexes)
- Production-quality code (testing, error handling, background services)

---

## Domain Model

### User
- Unique identifier
- Full name
- Email address (unique, used for login)
- Password (stored as a hash — never plain text)
- Role: `Admin` or `Member`
- Created date

### TaskItem
- Unique identifier
- Title (required, max 200 characters)
- Description (optional, long text)
- Status: `Todo`, `InProgress`, `Done`, `Cancelled`
- Priority: `Low`, `Medium`, `High`, `Critical`
- Assigned user (optional — a task can be unassigned)
- Created by (the user who created it)
- Created date
- Last updated date
- Due date (optional)

### Comment
- Unique identifier
- The task it belongs to
- The user who wrote it
- Content (text)
- Created date

---

## API Specification

### Authentication Endpoints

| Method | Route | Description | Auth Required |
|--------|-------|-------------|---------------|
| POST | `/api/auth/register` | Create a new user account | No |
| POST | `/api/auth/login` | Login and receive a JWT token | No |

### Task Endpoints

| Method | Route | Description | Auth Required |
|--------|-------|-------------|---------------|
| GET | `/api/tasks` | Get all tasks (paginated, filterable, sortable) | Yes |
| GET | `/api/tasks/{id}` | Get a single task by ID | Yes |
| POST | `/api/tasks` | Create a new task | Yes |
| PUT | `/api/tasks/{id}` | Update an existing task | Yes (owner or Admin) |
| DELETE | `/api/tasks/{id}` | Delete a task | Yes (Admin only) |
| PATCH | `/api/tasks/{id}/status` | Update just the status of a task | Yes |
| POST | `/api/tasks/{id}/assign` | Assign a task to a user | Yes (Admin only) |

### Comment Endpoints

| Method | Route | Description | Auth Required |
|--------|-------|-------------|---------------|
| GET | `/api/tasks/{id}/comments` | Get all comments for a task | Yes |
| POST | `/api/tasks/{id}/comments` | Add a comment to a task | Yes |
| DELETE | `/api/tasks/{id}/comments/{commentId}` | Delete a comment | Yes (owner or Admin) |

### User Endpoints

| Method | Route | Description | Auth Required |
|--------|-------|-------------|---------------|
| GET | `/api/users` | List all users | Admin only |
| GET | `/api/users/{id}` | Get a user profile | Yes |

---

## Query Parameters (for GET /api/tasks)

| Parameter | Type | Example | Purpose |
|-----------|------|---------|---------|
| `page` | int | `?page=2` | Page number (default: 1) |
| `pageSize` | int | `?pageSize=10` | Items per page (default: 10, max: 50) |
| `status` | string | `?status=InProgress` | Filter by status |
| `priority` | string | `?priority=High` | Filter by priority |
| `assignedTo` | guid | `?assignedTo=abc-123` | Filter by assigned user |
| `search` | string | `?search=login bug` | Search in title and description |
| `sortBy` | string | `?sortBy=dueDate` | Field to sort by |
| `order` | string | `?order=asc` or `?order=desc` | Sort direction |

---

## Response Conventions

- All responses use consistent JSON structure
- Successful list responses include pagination metadata in response headers (`X-Total-Count`, `X-Page`, `X-Page-Size`)
- Error responses include a `title`, `status`, and `detail` field (RFC 7807 Problem Details format)
- All dates are returned in ISO 8601 UTC format
- IDs are GUIDs

---

## Technical Stack

| Concern | Technology |
|---------|------------|
| Framework | ASP.NET Core (latest) |
| Language | C# (latest) |
| ORM | Entity Framework Core (primary) + Dapper (for complex queries — added Week 5) |
| Database | SQLite (Week 1) → SQL Server in Docker (Week 2+) |
| Authentication | JWT Bearer tokens |
| API Documentation | Swagger / OpenAPI (Swashbuckle) |
| Caching | `IMemoryCache` (Week 2) → consider distributed cache later |
| Testing | xUnit + Moq + WebApplicationFactory |
| Architecture | Layered (Week 1) → Clean Architecture (Week 4) |
| Messaging (Week 5) | RabbitMQ via MassTransit |
| Container | Docker + docker-compose |

---

## Solution Structure

Start simple. Evolve it as the weeks progress.

### Week 1 — Start here (single project, flat structure)

```
TaskManager/
├── TaskManager.sln
└── TaskManager.API/        ← One project to start. Everything lives here.
```

### Week 4 — Refactor to Clean Architecture

```
TaskManager/
├── TaskManager.sln
├── TaskManager.API/          ← HTTP layer: controllers, middleware, config, auth
├── TaskManager.Core/         ← Domain: entities, enums, interfaces (no dependencies)
├── TaskManager.Application/  ← Use cases: commands, queries, validators, MediatR handlers
├── TaskManager.Infrastructure/ ← Data: EF Core, repositories, caching, external services
└── TaskManager.Tests/        ← Unit tests + integration tests
```

---

## Week-by-Week Delivery Plan

### Week 1 — Foundation
- Project scaffolded via CLI (see Setup section below)
- `TaskItem` entity created and seeded with sample data
- EF Core configured with SQLite
- Full CRUD endpoints for tasks working
- Swagger UI accessible and showing all endpoints
- Global exception handling middleware in place
- Consistent error response format

### Week 2 — Pagination, Caching, Filtering
- All query parameters from the spec above working on `GET /api/tasks`
- `IMemoryCache` caching on the task list endpoint
- Cache invalidated on any write (create, update, delete)
- Database swapped from SQLite to SQL Server running in Docker
- `docker-compose.yml` added with API + SQL Server

### Week 3 — Authentication + Unit Tests
- `POST /api/auth/register` and `POST /api/auth/login` working
- JWT token returned on login, validated on protected routes
- `[Authorize]` applied to all task endpoints
- Role-based access: Admin-only endpoints locked down
- At least 15 unit tests covering the service layer
- One integration test using `WebApplicationFactory`

### Week 4 — Background Tasks + Clean Architecture
- `IHostedService` background worker that checks for overdue tasks every 5 minutes
- `POST /api/tasks/batch` endpoint that processes multiple tasks using `Task.WhenAll`
- Full Clean Architecture refactor (new project structure above)
- CQRS with MediatR: at least Create and Get task handlers converted

### Week 5 — SQL Performance + Rate Limiting
- Comment endpoints implemented
- Dapper used for the paginated task list query (CTE-based)
- Database indexes added on `Status`, `Priority`, `AssignedUserId`, `DueDate`
- Output caching with `[OutputCache]` on read-heavy endpoints
- Rate limiting middleware configured

### Week 6 — Full Test Suite
- All service layer methods covered by unit tests
- Happy path + not found + unauthorized + validation failure cases
- Integration tests for auth flow (register → login → access protected endpoint)
- `WebApplicationFactory` tests for at least 3 different endpoints

### Week 7 — AI / MCP Integration
- Option A: `/api/chat` endpoint powered by Semantic Kernel — answers natural language questions about tasks
- Option B: .NET MCP server exposing task tools (`list_tasks`, `create_task`, `complete_task`)

---

## Acceptance Criteria (Definition of Done per Week)

| Week | Done When |
|------|-----------|
| 1 | `dotnet run` starts the API, Swagger shows all CRUD endpoints, data persists to SQLite |
| 2 | `docker-compose up` starts everything, paginated + filtered task list works, cache visible in logs |
| 3 | Can register, login, use token to hit protected endpoint, rejected without token |
| 4 | Background service logs overdue tasks every 5 min, Clean Architecture layers compile independently |
| 5 | Dapper query returns paginated results, rate limiter returns 429 after threshold |
| 6 | All tests pass in CI pipeline, coverage report generated |
| 7 | AI endpoint answers "list my overdue tasks" or MCP tools callable from an AI client |

---

## Project Setup — CLI Commands

Run these commands in order to scaffold the project from scratch.

### Step 1 — Navigate to your working folder

```
cd R:\Learning\Career\TaskManager
```

### Step 2 — Create the API project

```
dotnet new webapi -n TaskManager.API --use-controllers
dotnet sln add TaskManager.API/TaskManager.API.csproj
```

### Step 3 — Verify it runs

```
cd TaskManager.API
dotnet run
```

Then open the URL shown in the terminal. You should see the default Swagger page.

### Step 4 — Install EF Core packages (run inside TaskManager.API folder)

```
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-ef
```

### Step 5 — Scaffold your first migration (after adding your DbContext)

```
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## Known Issue to Fix Before Starting

The solution file in this folder is named `ApiLearing.sln` — notice the typo (`Learing` instead of `Learning`).
Decide now whether to rename it or leave it. If you rename it, you will need to re-add any existing projects to the new solution file.

---

## Open Questions (Decide as You Build)

- Will tasks support attachments (files)? — Deferred for now
- Should comments support editing? — Deferred for now
- Will there be email notifications for task assignment? — Background service placeholder in Week 4, real email in a future phase
- `decimal` or `double` for any financial fields? — Not applicable to this domain
