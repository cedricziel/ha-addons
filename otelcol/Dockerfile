ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:6.1.3

FROM ghcr.io/open-telemetry/opentelemetry-collector-releases/opentelemetry-collector-contrib:0.105.0-amd64 as otelcol-amd64

FROM ghcr.io/open-telemetry/opentelemetry-collector-releases/opentelemetry-collector-contrib:0.105.0-arm64 as otelcol-arm64

FROM ghcr.io/tarampampam/curl:8.6.0 as curl

FROM scratch as container

COPY --from=otelcol-amd64 /otelcol-contrib /amd64/
COPY --from=otelcol-arm64 /otelcol-contrib /aarch64/

# hadolint ignore=DL3006
FROM $BUILD_FROM

# Setup base system
ARG BUILD_ARCH=amd64

# Copy root filesystem
COPY rootfs /

COPY --from=container "/${BUILD_ARCH}/otelcol-contrib" /usr/bin/otelcol-contrib

COPY --from=curl /bin/curl /bin/curl

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Cedric Ziel <mail@cedric-ziel.com>" \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant OpenTelemetry Collector AddOn" \
    org.opencontainers.image.authors="Cedric Ziel <mail@cedric-ziel.com>" \
    org.opencontainers.image.licenses="Apache2 License" \
    org.opencontainers.image.url="https://github.com/cedricziel/ha-addon-otelcol" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}

HEALTHCHECK --interval=5m --timeout=3s \
    CMD curl -f http://localhost:13133/ || exit 1