# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM=ghcr.io/home-assistant/amd64-base:3.15

# Tempo
# hadolint ignore=DL3029
FROM --platform=linux/amd64 grafana/tempo:2.5.0 as tempo-amd64
# hadolint ignore=DL3029
FROM --platform=linux/arm64 grafana/tempo:2.5.0 as tempo-arm64

FROM ghcr.io/tarampampam/curl:8.8.0 as curl

FROM scratch as container

COPY --from=tempo-amd64 /tempo /amd64/
COPY --from=tempo-arm64 /tempo /aarch64/

# hadolint ignore=DL3006
FROM $BUILD_FROM

# Setup base system
ARG BUILD_ARCH=amd64

COPY --from=container /${BUILD_ARCH}/tempo /tempo

COPY --from=curl /bin/curl /bin/curl

# Copy root filesystem
COPY rootfs /

HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost:3200/ready || exit 1
