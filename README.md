# TaskManager API — Daily Progress Tracker 🚀

**Goal:** Build a production-quality ASP.NET Core Web API from scratch, one small step per day, applying everything learned in Track A (CSharpWarmup).

**Project:** Track B of the 8-week interview readiness plan — a Task Management REST API modelled on tools like Jira/Trello.

**Repo:** `r:\Learning\Career\TaskManager\`

---

## 🤝 Mentoring Rules (How We Work Together on This Project)

> These rules govern every code review and hint session for the TaskManager API.
> The goal is to make you understand every line you write — not to hand you a finished project.

### What Copilot Will Do

- **Read your code first** before giving any feedback
- **Ask one targeted question** to check whether you understand what you just wrote
- **Give hints in layers** — the first hint points to the concept; the second hint narrows the search; only a third hint produces a fill-in snippet
- **Never write full implementations** — if you are stuck even after two hints, Copilot will show you a partial snippet with `// TODO:` blanks for you to complete
- **Explain the why after you solve it** — once you fix the issue yourself, a full explanation of the concept follows
- **Flag skipped concepts** — if you copy-paste something without understanding it, Copilot will ask you to explain it back before moving on

### What Copilot Will Not Do

- Generate a complete controller, service, or entity for you
- Tell you which NuGet package to install without first asking what you think you need
- Review code you did not write yourself
- Skip the hint phase even if you ask for "just the answer"

### The Three-Hint Rule

| Attempt | What You Get |
|---------|--------------|
| You try on your own | Full code review + one question |
| Hint 1 | The concept name + a pointer ("look at how `ActionResult<T>` works") |
| Hint 2 | A narrower redirect ("what does `NotFound()` return vs `null`?") |
| Hint 3 (last resort) | A partial snippet with blanks — you fill in the missing pieces |

### How to Ask for Help

| What you want | What to say |
|---------------|-------------|
| Something is wrong but you don't know what | "Review this" + paste the code |
| You have a theory | "I think the problem is X — am I right?" |
| Stuck after trying | "I tried X and Y, still failing — give me hint 1" |
| Want full explanation after solving | "I fixed it — now explain the concept fully" |
| Want concept theory only | "Explain [concept] without showing code" |

### The Learning Contract

- You attempt every task yourself before asking for help
- You read the full error message before asking — paste the exact error text when you ask
- You state what you think is wrong before Copilot responds
- When you solve something, you write a one-line comment in the code explaining what the problem was
- You do not move to the next day's task until today's "Done When" criteria are met

---

## 📖 How to Use This README

1. **Each day has one session** — roughly 1–2 hours. Do not try to rush multiple days into one.
2. **Read "What You'll Learn" first** — it tells you the concept before you write a single line.
3. **Follow the tasks in order** — each step builds on the previous one.
4. **Check off each task as you complete it** — `- [ ]` becomes `- [x]`.
5. **Hit "Done When" before moving on** — if the criteria are not met, the next day will be harder.
6. **Log your progress** — add one line to the Progress Log at the bottom of this file each day.

---

## 🗺️ What an API Actually Is (Read This First)

Before writing any code, understand the three-layer mental model you will build:

```
Browser / Swagger / Postman
        │
        │  HTTP Request (GET /api/tasks)
        ▼
┌─────────────────────────┐
│      Controller         │  ← Receives the request, picks what to do
│  (TasksController.cs)   │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│       Service           │  ← Contains the business logic (rules)
│  (TaskService.cs)       │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│      Repository /       │  ← Talks to the database
│      DbContext          │
└─────────────────────────┘
        │
        ▼
   SQLite / SQL Server
```

**HTTP Verbs you will use:**

| Verb | Meaning | Example |
|------|---------|---------|
| GET | Read data | Get all tasks |
| POST | Create new data | Create a task |
| PUT | Replace existing data | Update a whole task |
| PATCH | Partially update data | Change only the status |
| DELETE | Remove data | Delete a task |

**HTTP Status Codes you will return:**

| Code | Meaning | When to use |
|------|---------|-------------|
| 200 OK | Success | Successful GET, PUT, PATCH |
| 201 Created | New resource created | Successful POST |
| 204 No Content | Success, nothing to return | Successful DELETE |
| 400 Bad Request | Client sent invalid data | Validation failure |
| 401 Unauthorized | Not logged in | Missing or invalid token |
| 403 Forbidden | Logged in but not allowed | Wrong role |
| 404 Not Found | Resource doesn't exist | Task ID not in DB |
| 429 Too Many Requests | Rate limit exceeded | Week 5+ |
| 500 Internal Server Error | Bug in your code | Unhandled exception |

---

## 🗂️ Solution Structure (Evolves Weekly)

### Week 1 — Single project (flat structure)
```
TaskManager/
├── TaskManager.sln
└── TaskManager.API/
    ├── Controllers/        ← Your endpoint classes live here
    ├── Models/             ← Entities (TaskItem, User, Comment)
    ├── Services/           ← Business logic
    ├── Data/               ← DbContext (EF Core)
    ├── Middleware/         ← Error handling (Week 1 Day 5)
    ├── DTOs/               ← Request/Response shapes (Week 1 Day 4)
    ├── Program.cs          ← App startup + service registration
    └── appsettings.json    ← Configuration (connection string, JWT secret)
```

### Week 4 — Clean Architecture (you will refactor into this)
```
TaskManager/
├── TaskManager.sln
├── TaskManager.API/            ← HTTP layer only: controllers, middleware, config
├── TaskManager.Core/           ← Domain: entities, enums, interfaces (zero dependencies)
├── TaskManager.Application/    ← Use cases: MediatR commands, queries, validators
├── TaskManager.Infrastructure/ ← EF Core, repositories, caching, external services
└── TaskManager.Tests/          ← xUnit unit tests + WebApplicationFactory integration tests
```

---

## 📅 Daily Task Plan

> Each day = one focused session of 1–2 hours.
> Check off tasks as you complete them. Log your work in the Progress Log at the bottom.

---

### WEEK 1 — Foundation: Get the API Running with Full CRUD

**Week Goal:** By Friday, `dotnet run` starts the API, Swagger shows all task endpoints, and data saves to SQLite.

---

#### Day 1 — Create an Empty Web API Project and Understand the Structure

**What You'll Learn:** What ASP.NET Core generates, what `Program.cs` does, how to wire up controller support manually.

**Concept:** You will NOT use any scaffolding or generated controllers. You will create an empty web API project and add every piece by hand. This way you understand why each line exists. `Program.cs` is the entry point — it has two phases: *register services* (`builder.Services.*`) and *configure the HTTP pipeline* (`app.Use*`). Controllers are not active by default in a minimal project — you opt in by adding two lines yourself.

**Tasks:**
- [x] Run the scaffold commands (see CLI Quick Reference at the bottom)
- [x] Open `Program.cs` and read every line — answer these before moving on:
  - What is `builder.Services`?
  - What is the difference between the code before `var app = builder.Build()` and after it?
  - What does `app.Run()` do?
- [x] The project starts as a minimal API. Add controller support manually:
  - Add `builder.Services.AddControllers();` before `builder.Build()`
  - Add `app.MapControllers();` before `app.Run()`
- [x] Create an empty `Controllers/` folder inside `TaskManager.API/` — do not put anything in it yet
- [x] Run `dotnet run --project TaskManager.API` — it should start with no errors
- [x] Open the Swagger URL in the browser — Swagger will show no endpoints yet (that is expected)
- [x] Answer aloud: "What is a controller?" and "What does `[ApiController]` do?" — look it up if unsure

**Done When:**
- `dotnet run` starts without errors
- Swagger opens at `https://localhost:XXXX/swagger` (no endpoints yet — that is fine)
- You can explain the two lines you added and why each one is needed

**Common Pitfall:** If Swagger doesn't open, check whether the URL in the terminal starts with `http` or `https`. Try both. Also confirm `app.UseSwagger()` and `app.UseSwaggerUI()` are present in `Program.cs`.

---

#### Day 2 — Create the TaskItem Entity and Enums

**What You'll Learn:** What an entity is, C# `enum`, auto-implemented properties, data annotations.

**Concept:** An entity is a plain C# class whose properties map to columns in a database table. EF Core reads these classes and creates the table schema from them. A `Guid` is used instead of `int` for IDs because it can be generated client-side and is globally unique across systems.

**Tasks:**
- [ ] Create folder `TaskManager.API/Models/`
- [ ] Create `TaskStatus.cs` inside Models — define enum values: `Todo`, `InProgress`, `Done`, `Cancelled`
- [ ] Create `TaskPriority.cs` inside Models — define: `Low`, `Medium`, `High`, `Critical`
- [ ] Create `TaskItem.cs` inside Models — add all properties from the Domain Model section in REQUIREMENTS.md
  - Use `Guid` for Id, generate a default with `Guid.NewGuid()`
  - Use `DateTime` for CreatedDate, set default with `DateTime.UtcNow`
  - Use `string?` (nullable) for optional fields (Description, AssignedUserId, DueDate)
  - Add `[MaxLength(200)]` on the Title property
- [ ] Build the project: `dotnet build` — fix any compiler errors

**Done When:**
- `dotnet build` succeeds with zero errors
- You can explain: "Why Guid instead of int for the ID?"
- You can explain: "What does `string?` mean vs `string`?"

**Common Pitfall:** Forgetting `using System.ComponentModel.DataAnnotations;` for `[MaxLength]`. The compiler error will tell you — read it carefully.

---

#### Day 3 — EF Core + DbContext + First Migration

**What You'll Learn:** What a DbContext is, how EF Core maps classes to tables, what a migration is.

**Concept:** `DbContext` is the bridge between your C# objects and the database. You declare `DbSet<TaskItem>` inside it — EF Core uses that to know it should manage a `TaskItems` table. A migration is a snapshot of your model changes, translated into SQL `CREATE TABLE` / `ALTER TABLE` statements.

**Tasks:**
- [ ] Install EF Core packages (see CLI Quick Reference — EF Core section)
- [ ] Create `TaskManager.API/Data/AppDbContext.cs`
  - Inherit from `DbContext`
  - Add a constructor that accepts `DbContextOptions<AppDbContext>`
  - Add `public DbSet<TaskItem> Tasks { get; set; }`
- [ ] In `Program.cs`, register the DbContext before `builder.Build()`:
  - Use `AddDbContext<AppDbContext>` with SQLite
  - Connection string: `"Data Source=taskmanager.db"`
- [ ] Run the first migration: `dotnet ef migrations add InitialCreate --project TaskManager.API`
- [ ] Apply it: `dotnet ef database update --project TaskManager.API`
- [ ] Verify: a `taskmanager.db` file appears in the project folder

**Done When:**
- `dotnet ef database update` runs without error
- `taskmanager.db` file exists on disk
- You can explain: "What does a migration file contain and why shouldn't you edit it manually?"

**Common Pitfall:** `dotnet ef` command not found — you need to install it globally first: `dotnet tool install --global dotnet-ef`. If you already installed it, check your PATH.

---

#### Day 4 — Your First Endpoint: GET /api/tasks (The List)

**What You'll Learn:** How a controller file is structured, how to query EF Core, `ActionResult<T>`, your first working endpoint.

**Concept:** Start with just the list endpoint. No DTOs yet, no service layer — query `AppDbContext` directly from the controller. This is intentionally flat. You will refactor it later. The goal today is to see the full path: HTTP request → controller method → EF Core query → JSON response. Once you can see that path clearly, adding layers on top makes sense.

`ActionResult<T>` is the return type for controller actions — it lets the method return either data (200 OK with the list) or a status code result (like `NotFound()`) without a compile error.

**Tasks:**
- [ ] Create `TaskManager.API/Controllers/TasksController.cs` manually (new empty file — no scaffolding)
  - Add `[ApiController]` attribute above the class
  - Add `[Route("api/tasks")]` attribute above the class
  - Inject `AppDbContext` via the constructor
- [ ] Implement `GET /api/tasks` — for now, return the `TaskItem` entities directly (no DTO yet):
  - Method signature: `public async Task<ActionResult<IEnumerable<TaskItem>>> GetAll()`
  - Body: query all tasks from DbContext with `await _context.Tasks.ToListAsync()`
  - Return `Ok(tasks)`
- [ ] Run `dotnet run` and open Swagger — `GET /api/tasks` should now appear
- [ ] Call it in Swagger — it returns an empty array `[]` (no tasks in the DB yet — that is correct)
- [ ] Call `POST` directly on the SQLite DB (or use a DB browser tool) to insert one row manually, then call GET again — the inserted task should appear

**Done When:**
- `GET /api/tasks` appears in Swagger
- Calling it returns a valid JSON array (empty or with rows)
- `dotnet build` has zero errors or warnings
- You can trace the path aloud: "the request hits this method because of these two attributes, then this line runs the query, then this line wraps the result in a 200 response"

**What comes next:** Day 5 adds `GET /api/tasks/{id}` and `POST /api/tasks`. Day 6+ adds DTOs once you have seen why you need them.

**Common Pitfall:** Forgetting `async`/`await` and `ToListAsync()` — using `ToList()` (synchronous) blocks the thread. The compiler won't catch this; get into the habit now.

---

#### Day 5 — Remaining CRUD + Global Error Handling Middleware

**What You'll Learn:** PUT vs PATCH, what middleware is, how to return consistent error responses.

**Concept:** Middleware is code that runs on every request before it reaches your controller and after the response leaves. Think of it as a filter chain. Global error middleware catches any unhandled exception and converts it to a clean JSON error instead of a raw 500 HTML page. `PATCH` only updates one field; `PUT` replaces the whole resource.

**Tasks:**
- [ ] Create `UpdateTaskRequest.cs` DTO (Title, Description, Priority, DueDate, AssignedUserId)
- [ ] Create `UpdateTaskStatusRequest.cs` DTO (Status only)
- [ ] Implement `PUT /api/tasks/{id}` — update all fields, return 404 if not found, 204 No Content on success
- [ ] Implement `DELETE /api/tasks/{id}` — delete, return 404 if not found, 204 No Content on success
- [ ] Implement `PATCH /api/tasks/{id}/status` — update only Status field
- [ ] Create `TaskManager.API/Middleware/ExceptionHandlingMiddleware.cs`
  - Wrap the `next(context)` call in try/catch
  - On exception: set response status to 500, write JSON with `title`, `status`, `detail` fields
- [ ] Register middleware in `Program.cs` with `app.UseMiddleware<ExceptionHandlingMiddleware>()`
- [ ] Test: deliberately throw an exception in a controller and verify Swagger shows clean JSON (not HTML)

**Done When:**
- All 7 task endpoints appear in Swagger and work correctly
- Throwing an unhandled exception returns `{ "title": "...", "status": 500, "detail": "..." }` — not an HTML page
- `dotnet run` still starts without warnings
- You can explain: "What is the order of middleware in the pipeline and why does it matter?"

**Common Pitfall:** Forgetting `await context.SaveChangesAsync()` after creating/updating/deleting. The data won't persist — it will disappear on the next request.

---

### WEEK 2 — Pagination, Caching, Filtering, Docker

**Week Goal:** `GET /api/tasks` supports all query parameters; results are cached; the app runs in Docker with SQL Server.

---

#### Day 6 — Pagination (page + pageSize)

**What You'll Learn:** `Skip()` and `Take()` in LINQ, response headers, why you never return all rows.

**Concept:** Returning every row from a table is dangerous — if there are 10,000 tasks, you send 10,000 rows over the wire for every request. Pagination limits the response to a window (e.g., rows 11–20). `Skip((page - 1) * pageSize).Take(pageSize)` is the LINQ pattern. The total count goes in a response header so the client knows how many pages exist.

**Tasks:**
- [ ] Create `TaskManager.API/DTOs/TaskQueryParameters.cs` with `Page` (default 1) and `PageSize` (default 10, max 50)
- [ ] Update `GET /api/tasks` to accept `[FromQuery] TaskQueryParameters query`
- [ ] Apply `.Skip()` and `.Take()` to the EF Core query
- [ ] Add `X-Total-Count` response header with the total number of tasks (before pagination)
- [ ] Test: create 15 tasks via POST, then call `GET /api/tasks?page=2&pageSize=5` — should return tasks 11–15

**Done When:**
- `?page=2&pageSize=5` returns the correct window of results
- `X-Total-Count` header shows the real total
- `?pageSize=100` is capped at 50 (add validation in the DTO)

---

#### Day 7 — Filtering (status, priority, assignedTo, search)

**What You'll Learn:** Conditional LINQ chaining, `IQueryable<T>` deferred execution.

**Concept:** `IQueryable<T>` builds a SQL query lazily — each `.Where()` you chain adds a `WHERE` clause to the SQL, but nothing runs until you call `.ToListAsync()`. This means you can chain multiple optional filters without making multiple database calls.

**Tasks:**
- [ ] Add optional properties to `TaskQueryParameters`: `Status?`, `Priority?`, `AssignedTo` (Guid?), `Search`
- [ ] In the controller, build the query step by step — only add a `.Where()` if the parameter is not null/empty:
  - Filter by `Status` if provided
  - Filter by `Priority` if provided
  - Filter by `AssignedUserId` if provided
  - Filter where Title or Description contains `Search` if provided
- [ ] Test each filter individually in Swagger

**Done When:**
- `?status=Todo` returns only Todo tasks
- `?search=bug` returns only tasks with "bug" in the title or description
- Combining filters works: `?status=Todo&priority=High`

**Common Pitfall:** Case sensitivity. `"todo"` won't match `"Todo"` unless you use `EF.Functions.Like` or `.ToLower()`. Decide on one approach and be consistent.

---

#### Day 8 — Sorting + IMemoryCache

**What You'll Learn:** Dynamic sorting with LINQ, what caching is and when to use it.

**Concept:** A cache stores the result of an expensive operation (a database query) in memory so the next identical request is served instantly without hitting the database. The trade-off: cached data can become stale. A cache key uniquely identifies what was cached — include every query parameter in the key so `?status=Todo` and `?status=Done` don't return the same cached list.

**Tasks:**
- [ ] Add `SortBy` and `Order` (asc/desc) to `TaskQueryParameters`
- [ ] Apply sorting to the query: switch on `SortBy` value to choose which field to order by
- [ ] Register `IMemoryCache` in `Program.cs`: `builder.Services.AddMemoryCache()`
- [ ] Inject `IMemoryCache` into the tasks controller
- [ ] In `GET /api/tasks`: build a cache key from all query parameters, check the cache first, only query the DB on a cache miss, store the result with a 30-second expiry
- [ ] Add a log line: `"Cache hit"` vs `"Cache miss"` — observe in the terminal

**Done When:**
- `?sortBy=dueDate&order=asc` returns tasks ordered by due date
- Second identical request shows `"Cache hit"` in the terminal and returns instantly
- Different query parameters produce a different cache key (different cache entries)

---

#### Day 9 — Cache Invalidation

**What You'll Learn:** Why stale cache data is a real problem and how to fix it.

**Concept:** If you cache the task list but then create a new task, the cache still holds the old list. The next GET returns stale data. The simplest fix: clear (invalidate) the cache whenever you write to the database. More sophisticated strategies (cache tags, versioning) come later.

**Tasks:**
- [ ] In every write endpoint (POST, PUT, PATCH, DELETE) — after `SaveChangesAsync()` — remove or clear the relevant cache entries
- [ ] Use `IMemoryCache.Remove(key)` for specific keys, or introduce a cache key prefix so you can evict all task-list entries at once
- [ ] Test: POST a new task, immediately GET all tasks — the new task should appear (not a cached old list)

**Done When:**
- Creating a task clears the cache — the next GET returns fresh data from the DB
- You can explain: "What would go wrong if you did NOT invalidate the cache after a write?"

---

#### Day 10 — Docker + SQL Server

**What You'll Learn:** What Docker is, `docker-compose`, how to swap the database.

**Concept:** A Docker container is a lightweight isolated environment with everything needed to run your app. `docker-compose` starts multiple containers together (API + database) with one command. SQL Server is a production-grade database; SQLite is file-based and not suitable for production or multi-instance deployments.

**Tasks:**
- [ ] Create `TaskManager.API/Dockerfile` — multi-stage build: `dotnet restore` → `dotnet publish` → final runtime image
- [ ] Create `docker-compose.yml` at the solution root with two services: `db` (SQL Server image) and `api` (your Dockerfile)
- [ ] Update `appsettings.json` with a SQL Server connection string pointing to the `db` container
- [ ] Update `AppDbContext` registration in `Program.cs` to use `UseSqlServer` instead of `UseSqlite`
- [ ] Install EF Core SQL Server package: `Microsoft.EntityFrameworkCore.SqlServer`
- [ ] Run `docker-compose up --build` — wait for SQL Server to be ready, then check Swagger at `localhost:8080`
- [ ] Run EF migrations inside the container (or apply them from host pointed at Docker)

**Done When:**
- `docker-compose up` starts both containers
- Swagger is accessible at `http://localhost:8080/swagger`
- Creating a task via Swagger persists to SQL Server (verify by restarting the container — data should survive)

**Common Pitfall:** SQL Server takes ~15 seconds to start inside Docker. Your API may fail on startup if it connects before SQL Server is ready. Add a retry policy or a `depends_on` health check in docker-compose.

---

### WEEK 3 — Authentication + Unit Tests

**Week Goal:** Users can register and login; all task endpoints require a valid JWT token; 15+ unit tests pass.

---

#### Day 11 — User Entity + Register Endpoint

**What You'll Learn:** Password hashing, why plain-text passwords are a critical security flaw.

**Concept:** Never store a password. Store a *hash* of the password — a one-way transformation that cannot be reversed. When the user logs in, you hash what they type and compare the two hashes. `BCrypt` is the industry standard: it is intentionally slow (to resist brute-force attacks) and includes a random salt (so two users with the same password have different hashes).

**Tasks:**
- [ ] Create `TaskManager.API/Models/User.cs` — properties: Id (Guid), FullName, Email (unique), PasswordHash, Role (string: "Admin" or "Member"), CreatedDate
- [ ] Add `DbSet<User> Users` to `AppDbContext`
- [ ] Add a unique index on `Email` in `AppDbContext.OnModelCreating`
- [ ] Create a new migration and update the database
- [ ] Install `BCrypt.Net-Next` NuGet package
- [ ] Create `TaskManager.API/Controllers/AuthController.cs`
- [ ] Create `RegisterRequest.cs` DTO (FullName, Email, Password)
- [ ] Implement `POST /api/auth/register`: check email doesn't already exist (return 400 if it does), hash the password, save the user, return 201
- [ ] Test in Swagger: register two users with the same email — the second should return 400

**Done When:**
- Register works; the database stores a hash, not the plain password
- Duplicate email returns 400 Bad Request
- You can explain: "What is a salt and why does BCrypt add one automatically?"

---

#### Day 12 — Login + JWT Token Generation

**What You'll Learn:** What a JWT is, how it is structured, how to generate and sign one.

**Concept:** A JWT (JSON Web Token) is a Base64-encoded string in three parts: `header.payload.signature`. The payload contains *claims* — facts about the user (id, email, role). The signature is created with a secret key, so the server can verify the token was not tampered with. The client sends the token in every subsequent request header: `Authorization: Bearer <token>`.

**Tasks:**
- [ ] Install `Microsoft.AspNetCore.Authentication.JwtBearer` and `System.IdentityModel.Tokens.Jwt`
- [ ] Add JWT config to `appsettings.json`: `Jwt:Key` (a long random string), `Jwt:Issuer`, `Jwt:Audience`, `Jwt:ExpiresInMinutes`
- [ ] Create `LoginRequest.cs` DTO (Email, Password) and `LoginResponse.cs` DTO (Token, ExpiresAt)
- [ ] Implement `POST /api/auth/login`:
  - Find user by email (return 401 if not found)
  - Verify password with `BCrypt.Verify()` (return 401 if wrong)
  - Generate a JWT with claims: `sub` (user id), `email`, `role`
  - Return `LoginResponse` with the token
- [ ] Configure JWT authentication in `Program.cs` (before `app.Build()`)
- [ ] Test: login with correct credentials, copy the token, go to `jwt.io`, paste it — verify your claims appear in the payload

**Done When:**
- Login returns a JWT token
- Wrong password returns 401
- The JWT decoded at jwt.io shows the correct user claims
- You can explain: "Why does the token have an expiry? What happens when it expires?"

---

#### Day 13 — [Authorize] + Role-Based Access

**What You'll Learn:** Authentication vs Authorization, `[Authorize]`, role-based policies.

**Concept:** *Authentication* = verifying who you are (your token is valid). *Authorization* = verifying what you are allowed to do (your role permits this action). `[Authorize]` on a controller means "valid token required". `[Authorize(Roles = "Admin")]` means "valid token AND role must be Admin".

**Tasks:**
- [ ] Add `[Authorize]` to the top of `TasksController` — all task endpoints now require a token
- [ ] Add `app.UseAuthentication()` and `app.UseAuthorization()` to `Program.cs` (order matters: Authentication before Authorization)
- [ ] Add `[Authorize(Roles = "Admin")]` to `DELETE /api/tasks/{id}` and `POST /api/tasks/{id}/assign`
- [ ] Test in Swagger: call `GET /api/tasks` without a token — should return 401; call with a token — should return 200; call DELETE with a Member token — should return 403
- [ ] Add the `Authorize` button in Swagger (configure Swashbuckle to accept Bearer tokens)

**Done When:**
- All task endpoints return 401 without a token
- Admin endpoints return 403 for a Member role token
- You can explain: "What is the difference between 401 and 403?"

---

#### Day 14 — Unit Tests: TaskService Happy Paths

**What You'll Learn:** xUnit, Arrange-Act-Assert, what to mock and why, `Task.FromResult`.

**Concept:** Unit tests verify a single unit of logic in isolation. "In isolation" means you replace real dependencies (the database, external services) with *mocks* — objects that return whatever you tell them to. Moq is the mocking library. The pattern is always: **Arrange** (set up data + mocks), **Act** (call the method), **Assert** (check the result).

**Tasks:**
- [ ] Add `TaskManager.Tests` xUnit project to the solution
- [ ] Install `Moq` and `FluentAssertions` (optional but useful)
- [ ] Extract a `ITaskService` interface and `TaskService` class if not already separated from the controller
- [ ] Write these tests (each is one `[Fact]` method):
  - `GetById_ExistingId_ReturnsTask`
  - `GetById_NonExistentId_ReturnsNull`
  - `CreateTask_ValidInput_ReturnsSavedTask`
  - `UpdateTask_ValidInput_UpdatesFields`
  - `DeleteTask_ExistingId_ReturnsTrue`
- [ ] Run `dotnet test` — all tests green

**Done When:**
- 5+ tests pass with `dotnet test`
- You can explain: "Why do we mock the DbContext instead of using a real database in unit tests?"

---

#### Day 15 — Unit Tests: Error Paths + Integration Test

**What You'll Learn:** Testing failure cases, `WebApplicationFactory`, in-memory integration testing.

**Concept:** Happy path tests check that things work. Error path tests check that things *fail correctly* — the right status code, the right error message. Integration tests go through the full HTTP stack (middleware, routing, controller, service, database) using an in-memory test server — no real server needed.

**Tasks:**
- [ ] Write error path unit tests (aim for 10+ total tests):
  - `CreateTask_EmptyTitle_ThrowsValidationException`
  - `UpdateTask_NotFound_ReturnsFalse`
  - `DeleteTask_NotFound_ReturnsFalse`
  - `Register_DuplicateEmail_ReturnsBadRequest` (auth service)
  - `Login_WrongPassword_ReturnsNull` (auth service)
- [ ] Install `Microsoft.AspNetCore.Mvc.Testing`
- [ ] Write one `WebApplicationFactory` integration test that chains: register → login → use token to call `GET /api/tasks` → assert 200
- [ ] Run `dotnet test` — all 15+ tests green

**Done When:**
- `dotnet test` shows 15+ tests, all passing
- The integration test exercises the full HTTP pipeline
- You can explain: "What is the difference between a unit test and an integration test?"

---

### WEEK 4 — Background Tasks + Clean Architecture

**Week Goal:** A background service checks for overdue tasks every 5 minutes; the solution is refactored into Clean Architecture layers.

---

#### Day 16 — IHostedService Background Worker

**What You'll Learn:** `BackgroundService`, `CancellationToken`, how hosted services run independently of HTTP.

**Concept:** A `BackgroundService` runs for the lifetime of the application in a separate thread. It has no connection to HTTP requests — it wakes up on its own schedule. `CancellationToken` is how ASP.NET signals the service to stop gracefully when the app shuts down.

**Tasks:**
- [ ] Create `TaskManager.API/Services/OverdueTaskChecker.cs` inheriting from `BackgroundService`
- [ ] Override `ExecuteAsync(CancellationToken stoppingToken)` with a loop:
  - Query for tasks where `DueDate < DateTime.UtcNow` and `Status != Done` and `Status != Cancelled`
  - Log each overdue task title and ID
  - Wait 5 minutes: `await Task.Delay(TimeSpan.FromMinutes(5), stoppingToken)`
- [ ] Register in `Program.cs`: `builder.Services.AddHostedService<OverdueTaskChecker>()`
- [ ] Run the app and observe the log output in the terminal

**Done When:**
- Logs show "Checking for overdue tasks..." every 5 minutes
- Creating a task with a past due date causes it to appear in the next check
- You can explain: "What happens to this service when the app receives a shutdown signal?"

---

#### Day 17 — Task.WhenAll Batch Endpoint

**What You'll Learn:** `Task.WhenAll`, parallel async operations, when parallel is better than sequential.

**Concept:** Processing 10 tasks one after another takes 10× the time of one. `Task.WhenAll` starts all operations simultaneously and waits for all to complete — total time ≈ the slowest single operation. The trade-off: parallel writes can cause conflicts if two operations touch the same resource.

**Tasks:**
- [ ] Create `BatchCreateRequest.cs` DTO with `List<CreateTaskRequest> Tasks`
- [ ] Implement `POST /api/tasks/batch` in TasksController
  - Map each request to a `TaskItem`
  - Use `Task.WhenAll` to save all in parallel (or add all to context and `SaveChangesAsync` once)
  - Return 201 with a list of created task IDs
- [ ] Test: POST a batch of 5 tasks, verify all 5 are created

**Done When:**
- Posting 5 tasks in one request creates all 5
- You can explain: "Why might `Task.WhenAll` on the same DbContext instance cause problems?"

---

#### Day 18 — Clean Architecture: Core Layer

**What You'll Learn:** The Dependency Inversion Principle, why `Core` has zero external dependencies.

**Concept:** In Clean Architecture, the `Core` layer is the heart of the system — it knows about your business rules but nothing about ASP.NET, EF Core, or any framework. This means it is testable in complete isolation. Any other layer depends inward on `Core`; `Core` depends on nothing.

**Tasks:**
- [ ] Create a new `classlib` project: `TaskManager.Core`
- [ ] Move `Models/` (entities + enums) into `TaskManager.Core`
- [ ] Create `TaskManager.Core/Interfaces/ITaskRepository.cs` — declare methods: `GetAllAsync`, `GetByIdAsync`, `AddAsync`, `UpdateAsync`, `DeleteAsync`
- [ ] Create `TaskManager.Core/Interfaces/IUserRepository.cs`
- [ ] Add a project reference from `TaskManager.API` to `TaskManager.Core`
- [ ] Verify: `TaskManager.Core` has no reference to EF Core or Microsoft.AspNetCore packages

**Done When:**
- `dotnet build` compiles both projects
- `TaskManager.Core.csproj` has zero `PackageReference` entries (only the SDK)
- You can explain: "Why must Core not reference EF Core?"

---

#### Day 19 — Clean Architecture: Infrastructure Layer

**What You'll Learn:** How to implement an interface, what the Repository pattern solves.

**Concept:** `Infrastructure` implements the interfaces defined in `Core`. It *knows about* EF Core, but `Core` does not. When you swap the database (SQLite → SQL Server), only `Infrastructure` changes — `Core` and `API` stay the same. This is the Repository pattern in action.

**Tasks:**
- [ ] Create a new `classlib` project: `TaskManager.Infrastructure`
- [ ] Add reference to `TaskManager.Core` from `TaskManager.Infrastructure`
- [ ] Move `Data/AppDbContext.cs` into `TaskManager.Infrastructure`
- [ ] Create `TaskManager.Infrastructure/Repositories/TaskRepository.cs` implementing `ITaskRepository`
- [ ] In `Program.cs`, register: `services.AddScoped<ITaskRepository, TaskRepository>()`
- [ ] Update `TasksController` to inject `ITaskRepository` instead of `AppDbContext` directly
- [ ] Build and verify everything still works end-to-end

**Done When:**
- `TasksController` only knows about `ITaskRepository` — no `AppDbContext` in the controller
- All CRUD endpoints still work correctly
- You can explain: "What is the benefit of injecting an interface rather than a concrete class?"

---

#### Day 20 — CQRS with MediatR

**What You'll Learn:** CQRS pattern, MediatR, why separating reads from writes scales better.

**Concept:** CQRS (Command Query Responsibility Segregation) splits operations into Commands (writes that change state) and Queries (reads that do not change state). MediatR is a library that routes a request object to its handler — the controller no longer needs to know about services directly. This makes it trivial to add cross-cutting concerns (logging, validation, caching) in one place.

**Tasks:**
- [ ] Install `MediatR` NuGet package in `TaskManager.Application` (new classlib project) and `TaskManager.API`
- [ ] Create `GetTaskByIdQuery.cs` (record with `Guid Id`) and its `GetTaskByIdQueryHandler.cs`
- [ ] Create `CreateTaskCommand.cs` (record with task fields) and its `CreateTaskCommandHandler.cs`
- [ ] Register MediatR in `Program.cs`: `builder.Services.AddMediatR(...)`
- [ ] Update `GET /api/tasks/{id}` controller action to use `mediator.Send(new GetTaskByIdQuery(id))`
- [ ] Update `POST /api/tasks` controller action to use `mediator.Send(new CreateTaskCommand(...))`
- [ ] All other endpoints can stay as-is for now

**Done When:**
- GET by ID and POST both go through MediatR handlers
- You can explain: "What problem does MediatR solve that injecting a service directly does not?"

---

### WEEK 5 — SQL Performance + Rate Limiting

**Week Goal:** Comment endpoints work; the task list uses Dapper with a CTE; indexes are in place; rate limiting is active.

---

#### Day 21 — Comment Entity + GET + POST

**What You'll Learn:** One-to-many relationships in EF Core, navigation properties.

**Tasks:**
- [ ] Create `Comment.cs` entity in Core — properties from REQUIREMENTS.md domain model
- [ ] Add `DbSet<Comment>` to AppDbContext, configure foreign key to TaskItem
- [ ] Create migration + update DB
- [ ] Create `CommentsController` with `GET /api/tasks/{taskId}/comments` and `POST /api/tasks/{taskId}/comments`
- [ ] Test: create a task, add two comments, GET comments — both should appear

**Done When:** Both comment endpoints work; `GET` returns only comments for the specified task.

---

#### Day 22 — DELETE Comment

**What You'll Learn:** Owner/role checks inside an endpoint, extracting claims from the JWT.

**Tasks:**
- [ ] Implement `DELETE /api/tasks/{taskId}/comments/{commentId}`
- [ ] Extract the calling user's ID from `User.FindFirst(ClaimTypes.NameIdentifier)` in the controller
- [ ] Return 403 if the caller is not the comment author AND not an Admin
- [ ] Return 404 if comment doesn't exist
- [ ] Test: try deleting someone else's comment with a Member token — should return 403

**Done When:** Only the comment author or Admin can delete; correct status codes are returned for all cases.

---

#### Day 23 — Dapper CTE Pagination

**What You'll Learn:** When to use Dapper instead of EF Core, CTE syntax in SQL, raw SQL in .NET.

**Concept:** Dapper executes raw SQL and maps the results to C# objects — no query translation, full control. Use it when EF Core generates inefficient SQL or when you need features like CTEs, window functions, or stored procedures. The task list query with multiple filters + pagination + sorting is a good candidate.

**Tasks:**
- [ ] Install `Dapper`
- [ ] Create a `TaskQueryService.cs` (or add to Infrastructure repository) that uses `IDbConnection`
- [ ] Rewrite the paginated task list query using a SQL CTE:
  ```sql
  WITH FilteredTasks AS (
      SELECT *, COUNT(*) OVER() AS TotalCount
      FROM Tasks
      WHERE -- your filter conditions
  )
  SELECT * FROM FilteredTasks
  ORDER BY -- your sort column
  OFFSET @Skip ROWS FETCH NEXT @Take ROWS ONLY
  ```
- [ ] Wire it up so `GET /api/tasks` now calls this Dapper query
- [ ] Verify pagination still works correctly

**Done When:** `GET /api/tasks?page=2&pageSize=5` returns the correct page using Dapper; `X-Total-Count` header is correct.

---

#### Day 24 — Database Indexes

**What You'll Learn:** What a database index is, when it speeds things up, how to add one via EF Core.

**Concept:** An index is a separate data structure the database maintains to speed up lookups on a column. Without an index on `Status`, filtering `WHERE Status = 'Todo'` scans every row. With an index, the database jumps directly to the matching rows. The trade-off: indexes slow down writes slightly (because the index must also be updated).

**Tasks:**
- [ ] In `AppDbContext.OnModelCreating`, add indexes using `HasIndex()` on: `Status`, `Priority`, `DueDate`, `AssignedUserId`
- [ ] Create a migration: `dotnet ef migrations add AddTaskIndexes`
- [ ] Apply: `dotnet ef database update`
- [ ] Verify the migration SQL contains `CREATE INDEX` statements

**Done When:** Migration applies cleanly; you can explain which queries each index helps and which it doesn't.

---

#### Day 25 — Output Cache + Rate Limiting

**What You'll Learn:** Output caching vs memory caching, rate limiting middleware, HTTP 429.

**Concept:** Output caching caches the full HTTP response (headers + body) at the framework level — simpler than managing `IMemoryCache` manually. Rate limiting protects your API from abuse by capping how many requests a client can make in a time window. When the limit is exceeded, the server returns 429 Too Many Requests.

**Tasks:**
- [ ] Install `Microsoft.AspNetCore.OutputCaching` (built-in for .NET 7+)
- [ ] Add `builder.Services.AddOutputCache()` and `app.UseOutputCache()` in `Program.cs`
- [ ] Add `[OutputCache(Duration = 30)]` to `GET /api/tasks`
- [ ] Configure rate limiting: `builder.Services.AddRateLimiter()` with a fixed window policy (e.g., 100 requests per minute per IP)
- [ ] Add `app.UseRateLimiter()` to the pipeline
- [ ] Test: exceed 100 requests in a minute — should return 429

**Done When:** Second request to `GET /api/tasks` is served from output cache; exceeding rate limit returns 429.

---

### WEEK 6 — Full Test Suite

**Week Goal:** All service layer methods are covered by tests; integration tests cover the full auth flow; CI passes.

---

#### Day 26 — Unit Tests: Task Service Full Coverage

**Tasks:**
- [ ] Write tests for every public method in TaskService: `GetAll`, `GetById`, `Create`, `Update`, `Delete`, `UpdateStatus`
- [ ] Cover: found, not found, invalid input, boundary conditions (empty title, null ID)
- [ ] Aim for 10+ tests in this file alone

---

#### Day 27 — Unit Tests: Auth Service Full Coverage

**Tasks:**
- [ ] Write tests for: `Register_Success`, `Register_DuplicateEmail`, `Login_Success`, `Login_WrongPassword`, `Login_UserNotFound`
- [ ] Mock all dependencies; do not use a real database

---

#### Day 28 — Integration Tests: Auth Flow

**Tasks:**
- [ ] Write `WebApplicationFactory` test: `Register → Login → GET /api/tasks` (full happy path)
- [ ] Write: `Register → Login → DELETE task with Member token → expect 403`
- [ ] Write: `GET /api/tasks without token → expect 401`

---

#### Day 29 — Integration Tests: Task Endpoints

**Tasks:**
- [ ] `POST /api/tasks → GET /api/tasks → task appears`
- [ ] `POST /api/tasks → PUT /api/tasks/{id} → GET /api/tasks/{id} → updated values`
- [ ] `POST /api/tasks → DELETE /api/tasks/{id} (Admin token) → GET → 404`

---

#### Day 30 — Polish + Coverage Report

**Tasks:**
- [ ] Run `dotnet test --collect:"XPlat Code Coverage"` to generate a coverage report
- [ ] Fix any failing tests
- [ ] Ensure all tests pass in a clean build: `dotnet build && dotnet test`

**Done When:** All tests green; `dotnet test` output shows 15+ tests passing; coverage report generated.

---

### WEEK 7 — AI / MCP Integration

**Week Goal:** The API can answer natural language questions about tasks, or exposes tools callable by an AI agent.

---

#### Day 31 — Research + Decision

**Tasks:**
- [ ] Read Semantic Kernel docs: `learn.microsoft.com/semantic-kernel`
- [ ] Read MCP .NET SDK docs
- [ ] Decide: Option A (Semantic Kernel `/api/chat` endpoint) or Option B (MCP server exposing task tools)
- [ ] Log your decision and reasoning in the Progress Log

---

#### Days 32–35 — Build Your Chosen Option

**Option A — Semantic Kernel chat endpoint:**
- [ ] Install `Microsoft.SemanticKernel`
- [ ] Create `/api/chat` endpoint accepting `{ "question": "..." }`
- [ ] Connect the kernel to your task data: build a plugin that calls `ITaskRepository`
- [ ] Test: ask "What tasks are overdue?" — should return a natural language answer with real data

**Option B — MCP server:**
- [ ] Install the .NET MCP SDK
- [ ] Expose tools: `list_tasks`, `create_task`, `complete_task`
- [ ] Test by calling tools from an AI client (Claude Desktop or VS Code Copilot)

---

## 📋 API Endpoints Reference

### Authentication
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| POST | `/api/auth/register` | Create a new user account | No |
| POST | `/api/auth/login` | Login and receive a JWT token | No |

### Tasks
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| GET | `/api/tasks` | Get all tasks (paginated, filterable, sortable) | Yes |
| GET | `/api/tasks/{id}` | Get a single task by ID | Yes |
| POST | `/api/tasks` | Create a new task | Yes |
| PUT | `/api/tasks/{id}` | Update an existing task | Yes (owner or Admin) |
| DELETE | `/api/tasks/{id}` | Delete a task | Yes (Admin only) |
| PATCH | `/api/tasks/{id}/status` | Update just the status | Yes |
| POST | `/api/tasks/{id}/assign` | Assign a task to a user | Yes (Admin only) |
| POST | `/api/tasks/batch` | Create multiple tasks in parallel | Yes |

### Comments
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| GET | `/api/tasks/{id}/comments` | Get all comments for a task | Yes |
| POST | `/api/tasks/{id}/comments` | Add a comment to a task | Yes |
| DELETE | `/api/tasks/{id}/comments/{commentId}` | Delete a comment | Yes (owner or Admin) |

### Users
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| GET | `/api/users` | List all users | Admin only |
| GET | `/api/users/{id}` | Get a user profile | Yes |

---

## ✅ Week Completion Checkpoints

| Week | Done When |
|------|-----------|
| 1 | `dotnet run` starts, Swagger shows all 7 task endpoints, CRUD works, SQLite persists data |
| 2 | `docker-compose up` starts API + SQL Server; all query params work; cache visible in logs |
| 3 | Register → Login → use token → hit protected endpoint → rejected without token; 15+ tests pass |
| 4 | Background service logs overdue tasks every 5 min; Clean Architecture layers compile independently |
| 5 | Dapper query returns paginated results; rate limiter returns 429 after threshold |
| 6 | All tests pass in CI; coverage report generated |
| 7 | AI endpoint answers "list my overdue tasks" OR MCP tools callable from an AI client |

---

## 📋 API Endpoints Reference

### Authentication
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| POST | `/api/auth/register` | Create a new user account | No |
| POST | `/api/auth/login` | Login and receive a JWT token | No |

### Tasks
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| GET | `/api/tasks` | Get all tasks (paginated, filterable, sortable) | Yes |
| GET | `/api/tasks/{id}` | Get a single task by ID | Yes |
| POST | `/api/tasks` | Create a new task | Yes |
| PUT | `/api/tasks/{id}` | Update an existing task | Yes (owner or Admin) |
| DELETE | `/api/tasks/{id}` | Delete a task | Yes (Admin only) |
| PATCH | `/api/tasks/{id}/status` | Update just the status | Yes |
| POST | `/api/tasks/{id}/assign` | Assign a task to a user | Yes (Admin only) |
| POST | `/api/tasks/batch` | Process multiple tasks in parallel | Yes |

### Comments
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| GET | `/api/tasks/{id}/comments` | Get all comments for a task | Yes |
| POST | `/api/tasks/{id}/comments` | Add a comment to a task | Yes |
| DELETE | `/api/tasks/{id}/comments/{commentId}` | Delete a comment | Yes (owner or Admin) |

### Users
| Method | Route | Description | Auth |
|--------|-------|-------------|------|
| GET | `/api/users` | List all users | Admin only |
| GET | `/api/users/{id}` | Get a user profile | Yes |

---

## 🛠️ Technical Stack

| Concern | Technology |
|---------|------------|
| Framework | ASP.NET Core (latest) |
| Language | C# (latest) |
| ORM | Entity Framework Core + Dapper (Week 5+) |
| Database | SQLite (Week 1) → SQL Server in Docker (Week 2+) |
| Authentication | JWT Bearer tokens |
| API Docs | Swagger / OpenAPI (Swashbuckle) |
| Caching | `IMemoryCache` (Week 2) → Output Cache (Week 5) |
| Testing | xUnit + Moq + WebApplicationFactory |
| Architecture | Layered (Week 1) → Clean Architecture (Week 4) |
| Messaging | RabbitMQ via MassTransit (Week 5) |
| Container | Docker + docker-compose |

---

## 📝 Daily Progress Log (MM/DD/YYYY)

> Add one line here every day you work on this project. Be specific — say what you built, not just what day it was.

<!-- Format: - Week X · Day Y: [what you built] (MM/DD/YYYY) -->

---

## 🔧 CLI Quick Reference

### Scaffold the Project (Day 1 — One Time)

```bash
cd R:\Learning\Career\TaskManager

# Creates a minimal web API project — no auto-generated controllers, no WeatherForecast
dotnet new webapi -n TaskManager.API
dotnet sln add TaskManager.API/TaskManager.API.csproj
```

> You will manually add `builder.Services.AddControllers()` and `app.MapControllers()` to `Program.cs`.
> You will manually create every controller, every folder, and every file from here on.

### Run the API

```bash
dotnet run --project TaskManager.API
```

### Build

```bash
dotnet build
```

### Run Tests

```bash
dotnet test

# With coverage
dotnet test --collect:"XPlat Code Coverage"
```

### EF Core — Install Tools + Packages (Day 3)

```bash
# Install EF CLI globally (one time)
dotnet tool install --global dotnet-ef

# Add EF Core packages to the API project
dotnet add TaskManager.API/TaskManager.API.csproj package Microsoft.EntityFrameworkCore.Sqlite
dotnet add TaskManager.API/TaskManager.API.csproj package Microsoft.EntityFrameworkCore.Design

# Create a migration
dotnet ef migrations add InitialCreate --project TaskManager.API

# Apply migrations to the database
dotnet ef database update --project TaskManager.API
```

### Add a New Project to the Solution (Week 4)

```bash
# Create a class library
dotnet new classlib -n TaskManager.Core
dotnet sln add TaskManager.Core/TaskManager.Core.csproj

# Add a project reference
dotnet add TaskManager.API/TaskManager.API.csproj reference TaskManager.Core/TaskManager.Core.csproj
```

### Add Tests Project (Day 14)

```bash
dotnet new xunit -n TaskManager.Tests
dotnet sln add TaskManager.Tests/TaskManager.Tests.csproj
dotnet add TaskManager.Tests/TaskManager.Tests.csproj package Moq
dotnet add TaskManager.Tests/TaskManager.Tests.csproj package Microsoft.AspNetCore.Mvc.Testing
dotnet add TaskManager.Tests/TaskManager.Tests.csproj reference TaskManager.API/TaskManager.API.csproj
```

### Docker (Day 10)

```bash
# Build and start API + SQL Server
docker-compose up --build

# Run in background
docker-compose up -d

# Tear down
docker-compose down

# View API logs
docker-compose logs -f taskmanager-api
```

### Add a NuGet Package

```bash
# Replace PACKAGE_NAME with the actual package
dotnet add TaskManager.API/TaskManager.API.csproj package PACKAGE_NAME
```
