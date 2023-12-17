
# ASP.NET Core Grafana dashboards

> [!IMPORTANT]
> **The source code for the dashboards now lives at https://aka.ms/dotnet/grafana-source.**
>
> The dashboard JSON files in this repository are out of date and will eventually be deleted.

.NET 8 adds new metrics throughout ASP.NET Core using [System.Diagnostics.Metrics](https://learn.microsoft.com/dotnet/core/diagnostics/compare-metric-apis#systemdiagnosticsmetrics). Metrics is a modern API for reporting and collecting information about your apps.

If you're interested in trying out metrics, we've put together [Grafana](https://grafana.com/) dashboards that report ASP.NET Core metrics collected by [Prometheus](https://prometheus.io/). The dashboard JSON files are available in this repository, and you can [import the Dashboards into Grafana](https://grafana.com/docs/grafana/latest/dashboards/manage-dashboards/#import-a-dashboard).

## Screenshot

![ASP.NET Core Grafana dashboard](./dashboard-screenshot.png)
