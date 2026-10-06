# FixMyCampus local development

FixMyCampus uses the integrated modular ASP.NET Core .NET 10 API, PostgreSQL, and the existing Angular frontend. The previous SQL Server API is preserved under `backend/Legacy/FixMyCampus.Api`; the active solution is `backend/FixMyCampus.slnx`.

## Requirements

- .NET 10 SDK
- PostgreSQL available locally (the configured development port is `5432`)
- Node.js and npm
- HTTPS development certificate: `dotnet dev-certs https --trust`

## Configure local API secrets

The repository intentionally does not include database credentials or a JWT signing key. The API project is already configured for .NET user-secrets. From PowerShell at the repository root, set these values for your local PostgreSQL installation:

```powershell
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Host=localhost;Port=5432;Database=fixmycampus_integrated;Username=postgres;Password=<your-local-password>" --project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj
dotnet user-secrets set "Jwt:Secret" "<generate-a-random-secret-of-at-least-32-bytes>" --project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj
```

Use a different local PostgreSQL username/password if needed. Do not commit either value. The integrated database name is separate from the supplied backend's original database. Development startup applies the checked-in migrations and idempotently seeds demo accounts, buildings, and example tickets.

## Restore and build the backend

```powershell
dotnet restore .\backend\FixMyCampus.slnx
dotnet build .\backend\FixMyCampus.slnx
```

The initial EF Core migrations are already in `backend/FixMyCampus.Infrastructure/Persistence/Migrations`. To add a migration after changing the domain model:

```powershell
dotnet ef migrations add <MigrationName> --project .\backend\FixMyCampus.Infrastructure\FixMyCampus.Infrastructure.csproj --startup-project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj --context AppDbContext --output-dir Persistence\Migrations
```

Apply migrations to the configured local database:

```powershell
dotnet ef database update --project .\backend\FixMyCampus.Infrastructure\FixMyCampus.Infrastructure.csproj --startup-project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj --context AppDbContext
```

The API also applies migrations during Development startup.

## Run and test the API

```powershell
dotnet run --project .\backend\FixMyCampus.Api\FixMyCampus.Api.csproj --launch-profile https
```

The API listens at `https://localhost:7020` and `http://localhost:5211`. OpenAPI JSON is at `https://localhost:7020/openapi/v1.json`; the interactive Scalar API explorer is at `https://localhost:7020/scalar/v1`.

Seeded demo accounts:

- Administrator: `admin@hackathon.local` / `Admin123!`
- Reporter: `user@hackathon.local` / `User123!`
- Technician: `tech@hackathon.local` / `Tech123!`
- Pending reporter, for the approval workflow: `pending@hackathon.local` / `Pending123!`

The Angular app and API use credentialed HttpOnly access/refresh cookies. The API cookie settings support the Angular `http://localhost:4200` origin; do not disable HTTPS for API requests.

## Run the Angular frontend

In a second PowerShell window:

```powershell
Set-Location .\frontend
npm ci
npm start
```

Open `http://localhost:4200`. The API URL is configured in `frontend/src/environments/environment.ts`.

## Main workflows

- Reporters request accounts, submit reports, view only their own tickets, and confirm a reported fix.
- Administrators review user requests, view campus-wide ticket and dashboard data, assign technicians, and advance ticket status.
- Technicians use the work queue and update tickets assigned to them.
- Ticket status progression is enforced by the API: `New → Assigned → InProgress → Resolved`.
