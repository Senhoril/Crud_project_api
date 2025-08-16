# Task Manager API (ASP.NET Core 8 + EF Core + MySQL)

This is a simple Task Manager Web API built with ASP.NET Core 8, Entity Framework Core, and MySQL. It implements a CRUD for tasks (Tarefas) and exposes endpoints documented with Swagger.

Originally created as part of the DIO .NET track challenge, this project demonstrates building a clean REST API with persistence via EF Core.

## Tech Stack
- .NET 8 (ASP.NET Core Web API)
- Entity Framework Core 8
- MySQL with Pomelo.EntityFrameworkCore.MySql
- Swagger / OpenAPI (Swashbuckle)

## Project Structure
```
Crud_project_api/
├─ Controllers/
│  └─ TarefaController.cs
├─ Models/
│  ├─ Tarefa.cs
│  └─ EnumStatusTarefa.cs
├─ Context/
│  └─ OrganizadorContext.cs
├─ Migrations/
├─ Properties/
│  └─ launchSettings.json
├─ appsettings.json
├─ appsettings.Development.json
├─ Program.cs
├─ TrilhaApiDesafio.csproj
└─ TrilhaApiDesafio.sln
```

## Domain Model
Task (Tarefa) properties:
- id: integer
- titulo: string
- descricao: string
- data: string (ISO-8601 date/time)
- status: string enum ("Pendente" | "Finalizado")

Note: The API uses System.Text.Json with default ASP.NET Core JSON options (camelCase). Enums are serialized as strings.

Example JSON body:
```json
{
  "id": 0,
  "titulo": "My task",
  "descricao": "Details...",
  "data": "2025-08-17T10:00:00Z",
  "status": "Pendente"
}
```

## Endpoints
Base route: /Tarefa

- GET /Tarefa/{id}
- GET /Tarefa/ObterTodos
- GET /Tarefa/ObterPorTitulo?titulo=string
- GET /Tarefa/ObterPorData?data=YYYY-MM-DD
- GET /Tarefa/ObterPorStatus?status=Pendente|Finalizado
- POST /Tarefa
- PUT /Tarefa/{id}
- DELETE /Tarefa/{id}

Validation rules (server-side):
- POST /Tarefa
  - titulo: required, non-empty
  - descricao: required, non-empty
  - data: required; cannot be DateTime.MinValue; cannot be in the past
  - status: cannot be created with "Finalizado"
- PUT /Tarefa/{id}
  - data: cannot be DateTime.MinValue

## Requirements
- .NET SDK 8.0+
- MySQL Server (8.x recommended)
- Optional: dotnet-ef tool for CLI migrations

Install the EF tool (if needed):
```
dotnet tool install --global dotnet-ef
```

## Configuration
Edit appsettings.Development.json and set your connection string:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=Tarefas;User=root;Password=your_password;"
  }
}
```
For production, prefer environment variables or user secrets instead of committing passwords.

## Database & Migrations
This repository already contains migrations. To create the database/update schema:
```
dotnet ef database update --project "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj"
```
If you change the model, create a new migration:
```
dotnet ef migrations add YourMigrationName --project "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj"
dotnet ef database update --project "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj"
```

## Build & Run
Restore and build:
```
dotnet restore "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj"
dotnet build   "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj" -c Debug
```
Run:
```
dotnet run --project "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj"
```

Hot reload (watch):
```
dotnet watch --project "c:\\Users\\Administrador\\Documents\\vscode projects\\Crud_project_api\\TrilhaApiDesafio.csproj" run
```
Note: Passing the project to dotnet watch avoids the MSB1011 error in folders containing multiple projects/solutions.

## Swagger
Swagger UI is enabled in Development. Default URLs from launchSettings.json:
- https://localhost:7295/swagger
- http://localhost:5181/swagger

## Quick Usage (curl examples)
Create a task:
```
curl -X POST "https://localhost:7295/Tarefa" -H "Content-Type: application/json" -d '{
  "titulo": "Study EF Core",
  "descricao": "Read docs and practice",
  "data": "2025-08-17T10:00:00Z",
  "status": "Pendente"
}'
```

Get all tasks:
```
curl "https://localhost:7295/Tarefa/ObterTodos"
```

Get by title:
```
curl "https://localhost:7295/Tarefa/ObterPorTitulo?titulo=Study"
```

## Troubleshooting
- MSB1011: Specify the project file when building/running from a folder with multiple targets:
  - `dotnet build "...TrilhaApiDesafio.csproj"`
  - `dotnet run --project "...TrilhaApiDesafio.csproj"`
  - `dotnet watch --project "...TrilhaApiDesafio.csproj" run`
- MySQL connection refused/timeout:
  - Ensure MySQL service is running
  - Verify host, port, user, and password in the connection string
  - Ensure the database exists or run migrations
- Port conflicts: Stop other apps using the same ports or change `applicationUrl` in `Properties/launchSettings.json`.

## Notes
- This project is intended for learning purposes.
- No explicit license provided by the original challenge; add one if needed for distribution.
