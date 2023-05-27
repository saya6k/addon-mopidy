# Define ARG for base image
ARG BUILD_FROM=ghcr.io/hassio-addons/base:14.0.0

# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/ash", "-o", "pipefail", "-c"]

# Copy Python requirements file
COPY requirements.txt /tmp/

# Install necessary packages and setup base
# hadolint ignore=DL3018
RUN \
    apk add --no-cache \
        build-base \
        git \
        alsa-lib \
        gstreamer \
        gstreamer-plugins-bad \
        gstreamer-plugins-good \
        gstreamer-plugins-ugly \
        libffi \
        libxml2 \
        libxslt \
        nginx \
        python3 \
        py3-gobject3 \
        zlib \
    \
    && apk add --no-cache --virtual .build-deps \
        python3-dev \
        libffi-dev \
        libxml2-dev \
        libxslt-dev \
        zlib-dev \
    \
    && curl https://bootstrap.pypa.io/get-pip.py | python3 \
    && pip3 install --no-cache-dir -r /tmp/requirements.txt \
    \
    && find /usr/local \
        \( -type d -a -name test -o -name tests -o -name '__pycache__' \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    \
    && apk del .build-deps \
    && rm -rf \
        /var/cache/apk/* \
        /root/.cache \
    && find /tmp/ -mindepth 1 -delete

# Move access.log to stdout and error.log to stderr
RUN mkdir -p /var/log/nginx && ln -sf /dev/stdout /var/log/nginx/access.log && ln -sf /dev/stderr /var/log/nginx/error.log

# Copy root filesystem
COPY rootfs /

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
    maintainer="Bram Mittendorff, Franck Nijhof, Robbert Müller" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Bram Mittendorff, Franck Nijhof, Robbert Müller" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}

