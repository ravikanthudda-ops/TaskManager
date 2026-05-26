# API Learning — Feedback & Deferred Questions Log

> Tracks questions you could not answer, code review observations, and patterns to revisit.
> Come back to each open item, research it, then write your own answer in the Answer column.
> Only mark it Resolved once you can explain it without looking it up.

---

## How to Read This File

- **Open** — not yet answered
- **Attempted** — you gave a partial answer; a hint is recorded
- **Resolved** — you answered it correctly and can explain it confidently

---

## Deferred Conceptual Questions

### Day 1 — Program.cs & ASP.NET Core Fundamentals

| # | Question | Your First Attempt | Hint Given | Status | Your Answer (fill in) |
|---|----------|--------------------|------------|--------|-----------------------|
| 1 | What is `builder.Services`? | "middleware that registers our controllers" | `builder.Services` is not middleware. There is a software design pattern where a class receives its dependencies from outside rather than creating them itself. When your controller needs a DbContext, something will hand it one instead of the controller creating it. What is that pattern called? | Attempted | |
| 2 | What does `app.Run()` do? | "it helps us to run our controller" | Remove `app.Run()` from Program.cs, run the app, and observe what the process does. Does it stay running? What did `app.Run()` prevent? | Attempted | |
| 3 | What is the routing mechanism? | "user invokes controller from frontend" | A browser sends `GET /api/tasks` — an HTTP request to a URL. Your controller method signature is just `public IActionResult Get()`. Nothing in that signature is a URL. Something in ASP.NET Core maps the URL to the method. What is that mechanism called? | Attempted | |
| 4 | What does `[ApiController]` specifically do? | "tells compiler it handles HTTP verbs and no UI" | HTTP verbs are defined by `[HttpGet]`, `[HttpPost]` etc. — not by `[ApiController]`. Add a method that accepts `[FromBody] string title`, POST to it with an empty body. Note what HTTP status code you get. Then remove `[ApiController]` and try again. What changed? The automatic behavior you observed has a name — find it. | Attempted | |

---

## Code Review Observations

### Day 1 — TaskController.cs

| # | File | Observation | Hint | Status |
|---|------|-------------|------|--------|
| 1 | TaskController.cs | The `Get()` method has no HTTP verb attribute | Without `[HttpGet]`, this works only by naming convention — ASP.NET Core assumes a method named `Get` is a GET. That convention is fragile and breaks as soon as you add a second GET method. Explicit is always better than implicit in production code. What attribute should you add? | Resolved |
| 2 | TaskController.cs | Route resolves to `api/Task`, not `api/tasks` | `[Route("api/[controller]")]` takes the class name and removes the `Controller` suffix. Your class is `TaskController` → route segment becomes `Task`. The REST convention uses lowercase plural nouns. What are your two options to fix this? | Resolved |
| 3 | TaskController.cs | Namespace is `MyApp.Namespace` | This is the VS Code template default. It has no relation to your project. Namespaces should reflect the project structure. What should this namespace be? | Resolved |
| 4 | TaskController.cs | `using Microsoft.AspNetCore.Http` was unused | Nothing in the file needed the `Http` namespace. Dead `using` statements are noise — remove anything the compiler doesn't need. | Resolved |

### Day 1 — Swagger Setup (Program.cs)

| # | File | Observation | Hint | Status |
|---|------|-------------|------|--------|
| 1 | TaskManager.API/Program.cs | Swagger UI opened, but API definition failed to load with 404 on `/swagger/v1/swagger.json` | `app.UseSwaggerUI()` only renders the UI. The JSON endpoint is exposed by `app.UseSwagger()`. Both are required. | Resolved |
| 2 | TaskManager.API/TaskManager.API.csproj | Wrong package was added: `Swagger` instead of Swashbuckle | Remove `Swagger` and install `Swashbuckle.AspNetCore` for ASP.NET Core Swagger generation and UI support. | Resolved |
| 3 | TaskManager.API build process | Build failed with file lock errors (`TaskManager.API.exe` in use) after changes | Stop the running API process before rebuilding. File lock errors are process/runtime issues, not code syntax issues. | Resolved |

---

## Recurring Patterns

> Filled in as patterns emerge across multiple days.

| Pattern | First Seen | Times Seen | Notes |
|---------|------------|------------|-------|
| Requests for "give me code" should be answered with code in chat first, not direct file edits | Day 1 | 1 | User wants to type changes manually to improve memorization and retention. |

---

## Resolved Items

> Move items here once you can explain them confidently without looking them up.

| # | Question / Observation | Your Correct Answer | Day Resolved |
|---|------------------------|---------------------|--------------|
| 1 | Swagger UI shows "Failed to load API definition" | I had `UseSwaggerUI` but was missing `UseSwagger`, so `/swagger/v1/swagger.json` returned 404. Adding `app.UseSwagger();` before `app.UseSwaggerUI();` fixed it. | Day 1 |
| 2 | Correct package for Swagger in ASP.NET Core | The correct package is `Swashbuckle.AspNetCore`. The package named `Swagger` was incorrect for this project setup. | Day 1 |
