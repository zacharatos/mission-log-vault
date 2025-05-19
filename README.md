# Mission Log Vault
In the depths of the Shadow-Ops Tech Division, field agents need a lightweight, reliable vault to record and retrieve mission logs. Your first deliverable is the Mission Log Vault: a backend service to CRUD mission entries and a Python CLI client to interact with it. Think of it as your division’s digital black-box—simple, secure, and rock-solid.

## Project 1 Checklist: **Mission Log Vault**

Based on your choices (SQLite, API-key header, interactive REPL, UI in v2, Dockerize), here’s the step-by-step plan to go from an empty repo to MVP deployed:


#### 1. Repo & Solution Setup

* **Initialize Git repo**

  ```bash
  git init mission-log-vault
  cd mission-log-vault
  ```
* **Create solution and projects**

  * `dotnet new sln -n MissionLogVault`
  * `dotnet new webapi -n MissionLog.Api`
  * `dotnet new classlib -n MissionLog.Core`
  * `dotnet new xunit -n MissionLog.Tests`
  * `dotnet sln add **/*.csproj`


#### 2. Domain & Data Layer

* **Define `MissionLog` entity** in `MissionLog.Core`

  ```csharp
  public class MissionLog
  {
      public int    Id          { get; set; }
      public string Title       { get; set; }
      public string Description { get; set; }
      public DateTime Timestamp { get; set; }
      public string Author      { get; set; }
  }
  ```
* **Scaffold EF Core with SQLite**

  * Install packages in `MissionLog.Api`:

    ```
    dotnet add package Microsoft.EntityFrameworkCore.Sqlite
    dotnet add package Microsoft.EntityFrameworkCore.Design
    ```
  * Create `MissionLogContext : DbContext`, register `DbSet<MissionLog>`
* **Migrations & DB**

  ```bash
  dotnet ef migrations add InitialCreate --project MissionLog.Api
  dotnet ef database update --project MissionLog.Api
  ```

  **Resources:**

  * EF Core Getting Started (SQLite):
    [https://learn.microsoft.com/ef-core/get-started/?tabs=netcore-cli\:contentReference\[oaicite:0\]{index=0}](https://learn.microsoft.com/ef-core/get-started/?tabs=netcore-cli:contentReference[oaicite:0]{index=0})
  * Intro to EF Core Migrations:
    [https://www.youtube.com/watch?v=KnpJBjLrCB0](https://www.youtube.com/watch?v=KnpJBjLrCB0) (Microsoft Learn)


#### 3. Repository & Service Layer

* **Implement Repository Pattern**

  * Interface `IMissionLogRepository` (CRUD methods)
  * Concrete `MissionLogRepository` using `MissionLogContext`
* **Unit of Work (optional for v1)**
* **Register with DI** in `Startup.cs`/`Program.cs`


#### 4. API Key Header Middleware

* **Add custom middleware** to inspect `X-Api-Key` header

  * If missing or invalid, return **401 Unauthorized**
  * Store valid key(s) in `appsettings.json` or env var
* **Resources:**

  * Writing Custom Middleware:
    [https://docs.microsoft.com/aspnet/core/fundamentals/middleware\:contentReference\[oaicite:2\]{index=2}](https://docs.microsoft.com/aspnet/core/fundamentals/middleware:contentReference[oaicite:2]{index=2})
  * Simple API Key Example:
    [https://www.jerriepelser.com/blog/securing-asp-net-core-api-key-authentication/\:contentReference\[oaicite:3\]{index=3}](https://www.jerriepelser.com/blog/securing-asp-net-core-api-key-authentication/:contentReference[oaicite:3]{index=3})


#### 5. API Controllers

* **Create `MissionLogsController`**

  * Endpoints:

    * `GET /api/missionlogs`
    * `GET /api/missionlogs/{id}`
    * `POST /api/missionlogs`
    * `PUT /api/missionlogs/{id}`
    * `DELETE /api/missionlogs/{id}`
  * Input validation with DataAnnotations
  * Structured logging via Serilog
* **Integrate Serilog**

  * Install `Serilog.AspNetCore` and `Serilog.Sinks.Console`
  * Configure in `Program.cs`
  * **Resource:**
    Serilog Quick Start: [https://serilog.net/async/installation.html\:contentReference\[oaicite:4\]{index=4}](https://serilog.net/async/installation.html:contentReference[oaicite:4]{index=4})


#### 6. Unit Testing

* **Write xUnit tests** for:

  * Repository methods (use in-memory SQLite)
  * Controller actions (use `WebApplicationFactory<T>`)
* **Mock dependencies** with Moq or built-in test server
* **Resource:**

  * Testing ASP.NET Core APIs:
    [https://docs.microsoft.com/aspnet/core/test/integration-tests\:contentReference\[oaicite:5\]{index=5}](https://docs.microsoft.com/aspnet/core/test/integration-tests:contentReference[oaicite:5]{index=5})


#### 7. Python CLI: Interactive REPL

* **Structure** using Python’s `cmd` or `prompt_toolkit`
* **Commands:**

  * `list` → GET all logs
  * `create` → prompt for title/desc/author
  * `update <id>` → prompt fields
  * `delete <id>`
* **HTTP calls** with `requests`
* **Resource:**

  * Python `cmd` module tutorial:
    [https://docs.python.org/3/library/cmd.html\:contentReference\[oaicite:6\]{index=6}](https://docs.python.org/3/library/cmd.html:contentReference[oaicite:6]{index=6})
  * Building CLIs with `prompt_toolkit`:
    [https://realpython.com/python-prompt-toolkit/\:contentReference\[oaicite:7\]{index=7}](https://realpython.com/python-prompt-toolkit/:contentReference[oaicite:7]{index=7})


#### 8. Dockerization

* **API Dockerfile** (ASP.NET runtime + build multi-stage)
* **CLI Dockerfile** (Python 3 base, install `requests`)
* **docker-compose.yml** to spin up both, map ports, mount SQLite file
* **Resource:**

  * Dockerizing ASP.NET Core:
    [https://docs.microsoft.com/aspnet/core/host-and-deploy/docker\:contentReference\[oaicite:8\]{index=8}](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker:contentReference[oaicite:8]{index=8})
  * Dockerizing Python apps:
    [https://docs.docker.com/samples/python/\:contentReference\[oaicite:9\]{index=9}](https://docs.docker.com/samples/python/:contentReference[oaicite:9]{index=9})


#### 9. README & Deployment

* **Write README** with:

  * Project overview and theme tie-in
  * Setup, build, run, test, Docker instructions
* **Run end-to-end** via `docker-compose up`
