FROM --platform=$BUILDPLATFORM golang:1.17-alpine AS builder

ARG TARGETPLATFORM
ARG TARGETOS
ARG TARGETARCH
ARG VERSION
ARG CGO_ENABLED=on

ENV GOOS=$TARGETOS \
    GOARCH=$TARGETARCH

# Let's roll
RUN set -xe && \
    apk add --no-cache \
        --virtual .build-deps \
        curl \
        build-base \
        git \
        libcap && \
    mkdir -p /root/plugin && cd /root/plugin && \
    curl -sSL https://github.com/teddysun/v2ray-plugin/archive/v$VERSION.tar.gz | \
    tar xz --strip 1 && \
    go build -v -trimpath -ldflags "-X main.VERSION=v$VERSION -s -w -buildid=" -o /v2ray-plugin && \
    setcap 'cap_net_bind_service=+ep' /v2ray-plugin


FROM alpine:3.16

COPY --from=builder /v2ray-plugin /usr/bin/

CMD v2ray-plugin -version
