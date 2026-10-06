# Fix My Campus

Fix My Campus is a campus issue reporting and maintenance workflow platform designed for students, staff, and facilities teams. It lets reporters submit maintenance issues, administrators approve and manage accounts, and technicians resolve assigned tasks through a structured ticket lifecycle.

This project is organized as a full-stack web application with a .NET backend and an Angular frontend, backed by PostgreSQL and EF Core. The repository also includes a local development guide (`PROJECT_GUIDE.md`) that documents the setup, API configuration, and expected workflows for running the app in development.

## Project overview

The application focuses on the operational side of campus maintenance:

- Reporters can request access and submit service issues.
- Administrators manage user requests, review campus-wide reports, and assign technicians.
- Technicians work through a maintenance queue and update assigned tickets.
- Ticket status is enforced by the API through a clear lifecycle: `New -> Assigned -> InProgress -> Resolved`.

The result is a practical maintenance-tracking system for universities or campus environments, where issues are visible, assigned, and resolved in a controlled workflow.

## Tech stack

- Frontend: Angular 22
- Backend: ASP.NET Core Web API on .NET 10
- Database: PostgreSQL with EF Core
- Authentication: JWT + credentialed HttpOnly cookies for secure browser sessions
- API tooling: Swagger/OpenAPI + Scalar API explorer
- Development environment: local PostgreSQL, .NET user-secrets, Node.js/npm

## Architecture

The repo is split into two major application layers:

- `backend/` — the API project and infrastructure for the application
- `frontend/` — the Angular client that consumes the API

At the application level:

- The backend exposes secure endpoints for authentication, report creation, ticket assignment, status updates, and admin actions.
- The frontend renders the user interfaces for reporters, administrators, and technicians.
- EF Core models the database schema and migrations are available to initialize the PostgreSQL database.
- The API uses role-based workflows to enforce what each user type can do.

The supporting documentation in `PROJECT_GUIDE.md` describes the intended runtime setup, local configuration, and development best practices.

## Main user workflows

### Reporter workflow
- Request an account
- Submit a maintenance issue or campus problem
- View only their own tickets
- Confirm when a reported fix has been completed

### Administrator workflow
- Review and approve reporter requests
- View campus-wide dashboard data and active tickets
- Assign technicians to jobs
- Advance tickets through the approval and resolution process

### Technician workflow
- Receive assigned jobs from the work queue
- Update ticket status and progress
- Resolve issues and contribute to operational reporting

## Development setup

The project expects a local development environment with the following tools installed:

- .NET 10 SDK
- PostgreSQL running locally on port `5432`
- Node.js and npm
- HTTPS certificate trust for local ASP.NET development

### Configure secrets

The project intentionally does not commit sensitive values like database credentials and JWT signing keys. These should be stored using .NET user secrets.

```powershell
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Host=localhost;Port=5432;Database=fixmycampus_integrated;Username=postgres;Password=<your-local-password>" --project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj
dotnet user-secrets set "Jwt:Secret" "<generate-a-random-secret-of-at-least-32-bytes>" --project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj
```

### Restore and build the backend

```powershell
dotnet restore .\backend\FixMyCampus.slnx
dotnet build .\backend\FixMyCampus.slnx
```

### Run the API

```powershell
dotnet run --project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj --launch-profile https
```

The API is expected to run on:

- `https://localhost:7020`
- `http://localhost:5211`

OpenAPI is available at:

- `https://localhost:7020/openapi/v1.json`
- `https://localhost:7020/scalar` (interactive API browser)

### Run the frontend

```powershell
Set-Location .\frontend
npm ci
npm start
```

Then open:

- `http://localhost:4200`

## Demo accounts

The project includes seeded demo accounts for testing the workflow locally:

- Administrator: `admin@hackathon.local` / `Admin123!`
- Reporter: `user@hackathon.local` / `User123!`
- Technician: `tech@hackathon.local` / `Tech123!`
- Pending reporter: `pending@hackathon.local` / `Pending123!`

## Strengths of the project

- Clear role-based user journey for campus operations
- Full-stack implementation with a modern frontend and robust backend
- Strong ticket lifecycle enforcement through API rules
- Local development setup includes seeded demo access for validation
- Practical domain model aligned with real-world maintenance operations

## Considerations

This project is a development-focused implementation rather than a production-ready enterprise platform. Some configuration details are intentionally left out of the repo for security, such as database credentials and JWT secrets, and local setup is expected to be completed on a developer machine.

## Summary

Fix My Campus is a focused maintenance management application for campuses, balancing user reporting, administrative approval, and technician operations. It is a well-scoped full-stack project with a clear domain model, strong workflow logic, and a practical local-development setup that makes it easy to evaluate and extend.

For more implementation details, consult the project guide in `PROJECT_GUIDE.md`.
