# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:6.0.0

# Mimir
# hadolint ignore=DL3029
FROM --platform=linux/amd64 grafana/mimir:2.13.0 as mimir-amd64
# hadolint ignore=DL3029
FROM --platform=linux/arm64 grafana/mimir:2.13.0 as mimir-arm64

# Agent
# hadolint ignore=DL3029
FROM --platform=linux/amd64 telegraf:1.31.1 as telegraf-amd64
# hadolint ignore=DL3029
FROM --platform=linux/arm64/v8 telegraf:1.31.1 as telegraf-arm64

FROM scratch as container

COPY --from=mimir-amd64 /bin/mimir /amd64/
COPY --from=mimir-arm64 /bin/mimir /aarch64/

COPY --from=telegraf-amd64 /usr/bin/telegraf /amd64/
COPY --from=telegraf-arm64 /usr/bin/telegraf /aarch64/

# hadolint ignore=DL3006
FROM $BUILD_FROM

# Setup base system
ARG BUILD_ARCH=amd64

COPY --from=container /${BUILD_ARCH}/mimir /bin/mimir
COPY --from=container /${BUILD_ARCH}/telegraf /bin/telegraf

# Copy root filesystem
COPY rootfs /
