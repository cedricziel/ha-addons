# Home Assistant Add-on: Grafana Loki

## How to use

This add-on will start Grafana Loki with local storage.

It exposes itself on port `3100`.

To add it as a datasource to Grafana, choose `Loki` as type and use `http://390c6529-loki:3100` as URL.

Example to use it with the [OpenTelemetry collector add-on](https://github.com/cedricziel/ha-addon-opentelemetry/tree/main/otelcol), shipping a local log file:

HomeAssistant OpenTelemetry collector:

```yaml
receivers:
    otlp:
      protocols:
        grpc:
        http:
  processors:
    batch:
  exporters:
    logging:
      logLevel: debug
    loki:
      endpoint: http://local-loki:3100/loki/api/v1/push
      labels:
        attributes:
          sev: "severity"
  extensions:
    pprof:
    zpages:
  service:
    extensions: [pprof,zpages]
    pipelines:
      traces:
        receivers: [otlp]
        processors: [batch]
        exporters: [logging]
      metrics:
        receivers: [otlp]
        processors: [batch]
        exporters: [logging]
      logs:
        receivers: [otlp]
        processors: [batch]
        exporters: [logging,loki]
```

Shipping collector (on your local host):

```yaml
exporters:
  otlp:
    endpoint: localhost:4317
    tls:
      insecure: true

receivers:
  filelog:
    include:
      - "./*.log"
    include_file_name: false
    include_file_path_resolved: true
    operators:
      - type: regex_parser
        regex: '^(?P<time>\d{4}-\d{2}-\d{2}) (?P<sev>[A-Z]*) (?P<msg>.*)$'
        timestamp:
          parse_from: attributes.time
          layout: "%Y-%m-%d"
        severity:
          parse_from: attributes.sev
    start_at: beginning

service:
  pipelines:
    logs:
      receivers: [filelog]
      exporters: [otlp]
```
