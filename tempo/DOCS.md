# Home Assistant Add-on: Grafana Tempo

Grafana Tempo is an open source, easy-to-use, and high-scale distributed tracing backend.

## How to use

To send data to Tempo, you can use OTLP, Jaeger and Zipkin.

The hostname is `8047bde0-tempo`.

To send OpenTelemetry signals to Tempo via OTLP, you could use the following, very simple bit of configuration:

**OpenTelemetry Collector**

```yaml
receivers:
  otlp:
    protocols:
      grpc:
exporters:
  otlp/tempo:
    endpoint: 8047bde0-tempo:4317
    tls:
      insecure: true
service:
  pipelines:
    traces:
      exporters: [otlp/tempo]
```

**Workloads**

```shell
export OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=8047bde0-tempo:4317
export OTEL_EXPORTER_OTLP_TRACES_INSECURE=true
```
