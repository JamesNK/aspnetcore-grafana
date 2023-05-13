# ASP.NET Core Grafana dashboards

.NET 8 adds new metrics throughout ASP.NET Core using [System.Diagnostics.Metrics](https://learn.microsoft.com/dotnet/core/diagnostics/compare-metric-apis#systemdiagnosticsmetrics). Metrics is a modern API for reporting and collecting information about your apps.

If you're interested in trying out metrics, we've put together [Grafana](https://grafana.com/) dashboards that report ASP.NET Core metrics collected by [Prometheus](https://prometheus.io/). The dashboard JSON files are available in this repository and the dashboards can be [imported into Grafana](https://grafana.com/docs/grafana/latest/dashboards/manage-dashboards/#import-a-dashboard).

## Dashboards

* [ASP.NET Core.json](./dashboards/ASP.NET%20Core.json) - Overview of the ASP.NET Core app
* [ASP.NET Core Endpoint.json](./dashboards/ASP.NET%20Core%20Endpoint.json) - Detail for a specific ASP.NET Core endpoint

## Screenshot

![ASP.NET Core Grafana dashboard](./dashboard-screenshot.png)

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
app.MapPrometheusScrapingEndpoint();

app.Run();
```

Verify OpenTelemetry has been successfully configured by browsing to the `/metrics` endpoint on your app. It will return metrics data formatted to be scraped by Prometheus.

## Configure Prometheus and Grafana

For information about setting up Prometheus and Grafana, see [View metrics in Grafana with OpenTelemetry and Prometheus](https://learn.microsoft.com/dotnet/core/diagnostics/metrics-collection#view-metrics-in-grafana-with-opentelemetry-and-prometheus).
