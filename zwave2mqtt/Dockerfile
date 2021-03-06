ARG BUILD_FROM=hassioaddons/base:8.0.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
# hadolint ignore=DL3003
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base=0.5-r2 \
        git=2.26.2-r0 \
        linux-headers=5.4.5-r1 \
        npm=12.18.4-r0 \
        openzwave-dev=1.6.974-r0 \
        python2-dev~=2.7 \
        yarn=1.22.4-r0 \
    \
    && apk add --no-cache \
        eudev=3.2.9-r3 \
        libusb=1.0.23-r0 \
        lua-resty-http=0.15-r0 \
        nginx-mod-http-lua=1.18.0-r0 \
        nginx=1.18.0-r0 \
        nodejs=12.18.4-r0 \
        openssl=1.1.1g-r0 \
        openzwave=1.6.974-r0 \
    \
    && curl -J -L -o /tmp/openzwave-db.tar.gz \
        "https://github.com/OpenZWave/open-zwave/archive/189b9050ee857bc177a26973cd99b81ebc9fcd92.tar.gz" \
    && mkdir /tmp/db \
    && tar zxvf \
        /tmp/openzwave-db.tar.gz \
        --strip 1 -C /tmp/db \
    \
    && rm -f -r /etc/openzwave \
    && mkdir -p /etc/openzwave \
    && mv /tmp/db/config/* /etc/openzwave \
    \
    && curl -J -L -o /tmp/zwave2mqtt.tar.gz \
        "https://github.com/OpenZWave/Zwave2Mqtt/archive/v4.0.4.tar.gz" \
    && tar zxvf \
        /tmp/zwave2mqtt.tar.gz \
        --strip 1 -C /opt \
    \
    && cd /opt \
    && npm config set unsafe-perm \
    && npm install \
    && npm run build \
    && npm prune --production \
    \
    && yarn global add modclean \
    && modclean \
        --path /opt \
        --no-progress \
        --keep-empty \
        --run \
    && yarn global remove modclean \
    && yarn cache clean \
    \
    && apk del --no-cache --purge .build-dependencies \
    \
    && rm -f -r \
        /etc/nginx \
        /root/.node-gyp \
        /root/.npm \
        /tmp/.[!.]* \
        /tmp/* \
        /usr/lib/node_modules \
        /usr/local/share/.cache

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="Z-Wave to MQTT" \
    io.hass.description="Fully configurable Z-Wave to MQTT gateway and control panel" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="Z-Wave to MQTT" \
    org.opencontainers.image.description="Fully configurable Z-Wave to MQTT gateway and control panel" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/hassio-addons/addon-zwave2mqtt" \
    org.opencontainers.image.documentation="https://github.com/hassio-addons/addon-zwave2mqtt/blob/master/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
