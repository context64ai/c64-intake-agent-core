# Intake Agent Core

This package provides the core functionality for Intake Agents, offering the following key features:

- Implementation of the contract required by Data Context Hub
- Built-in admin functionality using a Blazor UI with [Radzen](https://blazor.radzen.com/get-started?theme=material3) components
- Keycloak integration for Single Sign-On (SSO) across a Data Context Hub instance

## Environment Variables

Configuration is managed primarily through environment variables:

| Variable                      | Description                                                         | Default |
|-------------------------------|---------------------------------------------------------------------|---------|
| `BASE_PATH`                   | The path where the intake agent will be hosted                      | `/`     |
| `DCH_BASE_URL`                | The base URL of the DCH instance (e.g., `https://dch.example.com/`) | -       |
| `DCH_KEYCLOAK_REALM`          | The Keycloak realm used by the DCH instance                         | -       |
| `DCH_KEYCLOAK_URL`            | The Keycloak URL (e.g., `https://dch.example.com/auth/`)            | -       |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | OTLP endpoint (required when OpenTelemetry is activated)            | -       |
| `POSTGRES_HOST`               | PostgreSQL host address                                             | -       |
| `POSTGRES_PORT`               | PostgreSQL port number                                              | -       |
| `POSTGRES_USER`               | PostgreSQL username                                                 | -       |
| `POSTGRES_PASSWORD`           | PostgreSQL password                                                 | -       |
| `POSTGRES_DATABASE`           | Database name used by this Intake Agent                             | -       |

## Usage

In your Program.cs add the following:

```csharp
var configurator = new IntakeAgentCoreConfigurator(builder.Services, builder.Configuration)
{
    AgentName = "Your Intake Agent", 
    ServiceName = "your-intake-agent",
    Version = "0.1.0",
    KeycloakClientId = "your-intake-agent"
};
configurator.AddPostgreSqlPersistence();
configurator.AddOpenTelemetry(builder);
configurator.ConfigureAdminUi(adminUiOptions =>
{
    // If you want to add your own Blazor pages, reference your assembly and configure the left side menu here.
    // Supported icons can be found here: https://fonts.google.com/icons?icon.set=Material+Symbols
    adminUiOptions.AddAssembly(typeof(Program).Assembly);
    adminUiOptions.AddConfidentialMenuItem("upload", "Upload", "./upload");
});

builder.Services.AddIntakeAgentCore(configurator);
```

### Implementation

The core functionality is provided by the `IDataProvider` interface, which must be implemented by the Intake Agent.
