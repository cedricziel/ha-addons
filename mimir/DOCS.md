# Home Assistant Add-on: Grafana Mimir

Grafana Mimir is a distributed, horizontally scalable, and highly available long term storage for Prometheus.

## How to use

This add-on provides a simple, scalable metrics storage. Ideally used in combination with Grafana.

To include your local mimir instance as datasource in Grafana:

- Go to "Configuration" -> "Data sources"
- Click "Add data source"
- Select "Prometheus" (Mimir can be used as a drop-in replacement for Prometheus)
- Enter "http://24212397-mimir:8001/prometheus" as `URL` configuration

That's it!

## Scrape HomeAssistant metrics

By default, Mimir will be mostly empty. You can optionally enable scraping the core metrics and storing them
in Mimir.

To do that, you need to enable the `prometheus` integration. You can do this by adding the following snippet
to your `configuration.yaml` file:

```yaml
prometheus:
```

This exposes an API endpoint containing the metrics. Don't worry, it's protected through API keys.

Next, enable the scraping through the addon configuration. Restart the addon. That's it!

## OpenTelemetry

Mimir can natively ingest OpenTelemetry metrics.

```shell
export OTEL_OTLP_ENDPOINT=http://24212397-mimir:8001/otlp
```

Or configure your exporter like this:

```yaml
exporters:
  otlphttp:
    endpoint: http://24212397-mimir:8001/otlp
```
