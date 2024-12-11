ARG BUILD_FROM=ghcr.io/home-assistant/amd64-base:3.15

# Loki
# hadolint ignore=DL3029
FROM --platform=linux/amd64 grafana/loki:3.1.2 as loki-amd64
# hadolint ignore=DL3029
FROM --platform=linux/arm64 grafana/loki:3.1.2 as loki-arm64
# hadolint ignore=DL3029
FROM --platform=linux/arm grafana/loki:3.1.2 as loki-armv7

FROM ghcr.io/tarampampam/curl:8.11.1 as curl

FROM scratch as container

COPY --from=loki-amd64 /usr/bin/loki /amd64/
COPY --from=loki-arm64 /usr/bin/loki /aarch64/
COPY --from=loki-armv7 /usr/bin/loki /armv7/

# hadolint ignore=DL3006
FROM $BUILD_FROM

# Setup base system
ARG BUILD_ARCH=amd64

COPY --from=container /${BUILD_ARCH}/loki /usr/bin/loki

COPY --from=curl /bin/curl /bin/curl

# Copy root filesystem
COPY rootfs /

HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost:3100/ready || exit 1
