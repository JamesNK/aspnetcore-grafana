# ASP.NET Core Grafana dashboards

.NET 8 adds new metrics throughout ASP.NET Core using [System.Diagnostics.Metrics](https://learn.microsoft.com/dotnet/core/diagnostics/compare-metric-apis#systemdiagnosticsmetrics). Metrics is a modern API for reporting and collecting information about your apps.

If you're interested in trying out metrics, we've put together [Grafana](https://grafana.com/) dashboards that report ASP.NET Core metrics collected by [Prometheus](https://prometheus.io/). The dashboard JSON files are available in this repository and the dashboards can be [imported into Grafana](https://grafana.com/docs/grafana/latest/dashboards/manage-dashboards/#import-a-dashboard).

## Export metrics from ASP.NET Core

ASP.NET Core metrics need to be exported to Prometheus. You can do this by configuring the OpenTelemetry .NET client.

Add OpenTelemetry packages to the project file:

```xml
<ItemGroup>
  <PackageReference Include="OpenTelemetry.Exporter.Prometheus.AspNetCore" Version="1.5.0-alpha.1" />
  <PackageReference Include="OpenTelemetry.Extensions.Hosting" Version="1.5.0-alpha.2" />
</ItemGroup>
```

Configure OpenTelemetry at startup:

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddOpenTelemetry()
    .WithMetrics(builder =>
    {
        builder.AddPrometheusExporter();

        builder.AddMeter("Microsoft.AspNetCore.Hosting", "Microsoft.AspNetCore.Server.Kestrel");
        builder.AddView("request-duration",
            new ExplicitBucketHistogramConfiguration
            {
                Boundaries = new double[] { 0, 0.005, 0.01, 0.025, 0.05, 0.075, 0.1, 0.25, 0.5, 0.75, 1, 2.5, 5, 7.5, 10 }
            });
    });

var app = builder.Build();
app.UseOpenTelemetryPrometheusScrapingEndpoint();
app.MapPrometheusScrapingEndpoint();

app.Run();
```

Verify OpenTelemetry was successfully configured by browsing to the `/metrics` endpoint on your app to see the Prometheus scraping content.
