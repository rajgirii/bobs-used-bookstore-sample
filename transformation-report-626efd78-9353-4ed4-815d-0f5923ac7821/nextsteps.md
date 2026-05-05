# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The solution has been transformed with no build errors across all projects:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

Since no build errors were detected, the transformation appears to have been successful. The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore and Build the Solution

Run a full restore and build from the solution root to confirm a clean state:

```bash
dotnet restore
dotnet build --configuration Release
```

Ensure there are no warnings that could indicate compatibility issues, deprecated APIs, or missing references.

---

## 2. Run the Domain Unit Tests

Execute the test project to verify that all existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output for any failures or skipped tests. If tests fail, inspect whether the failures are due to behavioral differences introduced by the migration to cross-platform .NET (e.g., changes in string comparison, file path handling, or culture-sensitive operations).

---

## 3. Validate the Data Layer

The `Bookstore.Data` project likely contains database access logic. Perform the following checks:

- Confirm that any Entity Framework Core migrations are up to date by running:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj
```

- If the project uses a connection string, verify it is correctly configured in `appsettings.json` or environment variables for the target environment.
- Apply pending migrations to a local or development database:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj
```

---

## 4. Run the Web Application Locally

Start the `Bookstore.Web` project and manually verify core functionality:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Check the following:
- The application starts without runtime exceptions.
- Key pages and endpoints load correctly.
- Any authentication, authorization, or session handling works as expected.
- Static assets are served properly.

---

## 5. Review the CDK Project

The `Bookstore.Cdk` project appears to define infrastructure. Verify the following:

- All CDK constructs compile and synthesize correctly. If using AWS CDK for .NET, run:

```bash
cdk synth
```

- Confirm that any environment-specific configuration (e.g., account IDs, region settings, resource names) is correctly set for the target deployment environment.
- Review any IAM roles, policies, or resource configurations to ensure they align with the migrated application's requirements.

---

## 6. Check for Runtime Compatibility Issues

Even without build errors, runtime issues can exist. Pay attention to:

- **File paths**: Ensure no hardcoded Windows-style paths (`\`) exist; use `Path.Combine` or forward slashes instead.
- **Platform-specific APIs**: Search for any usage of Windows-only APIs (e.g., registry access, Windows identity) that may not function on Linux or macOS.
- **Configuration**: Verify that `appsettings.json`, environment variables, and secrets are correctly loaded in the cross-platform environment.
- **NuGet packages**: Confirm that all referenced packages support the target .NET version and are not marked as deprecated.

---

## 7. Deploy to the Target Environment

Once local validation is complete:

1. Publish the web application:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

2. Deploy the published output to the target hosting environment (e.g., a virtual machine, app service, or server).
3. Deploy infrastructure changes using the CDK project if applicable.
4. Perform a smoke test against the deployed environment to confirm the application is functioning correctly end to end.