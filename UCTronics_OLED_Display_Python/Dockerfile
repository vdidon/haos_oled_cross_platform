ARG BUILD_FROM

FROM ${BUILD_FROM}

# Default ENV
ENV \
    LANG="C.UTF-8" \
    DEBIAN_FRONTEND="noninteractive" \
    CURL_CA_BUNDLE="/etc/ssl/certs/ca-certificates.crt" \
    S6_BEHAVIOUR_IF_STAGE2_FAILS=2 \
    S6_CMD_WAIT_FOR_SERVICES=1

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Version et Architecture
ARG \
    BASHIO_VERSION \
    TEMPIO_VERSION \
    S6_OVERLAY_VERSION

# Base system
WORKDIR /usr/src

RUN set -x && apt-get update && apt-get install -y \
    gcc \
    procps \
    bash \
    jq \
    tzdata \
    curl \
    nano \
    ca-certificates \
    wget \
    python3 \
    python3-dev \
    python3-pip \
    python3-rpi.gpio \
    libjpeg-dev \
    libfreetype6-dev \
    libopenjp2-7 \
    xz-utils \
    build-essential \
    python3-setuptools

RUN wget "https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-aarch64.tar.xz" && \
    tar Jxvf s6-overlay-aarch64.tar.xz -C / && \
    mkdir -p /etc/fix-attrs.d /etc/services.d

RUN wget -O /usr/bin/tempio "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_aarch64" && \
    chmod a+x /usr/bin/tempio && \
    mkdir -p /usr/src/bashio


RUN mkdir -p /usr/src/bashio && \
    wget "https://github.com/hassio-addons/bashio/archive/v${BASHIO_VERSION}.tar.gz" && \
    tar -xzf v${BASHIO_VERSION}.tar.gz -C /usr/src/bashio --strip-components=1 && \
    mv /usr/src/bashio/lib /usr/bin

RUN rm -rf /var/lib/apt/lists/* /usr/src/*

# Install Pythong Libs for this Add-on
RUN python3 -m pip install --break-system-packages --upgrade pip
RUN python3 -m pip install --break-system-packages --no-cache-dir \
    requests \
    gpiozero \
    adafruit-blinka \
    adafruit-circuitpython-ssd1306 \
    pillow \
    websocket-client

COPY /python* /UCTronics_OLED/

# S6-Overlay
COPY start.sh /
WORKDIR /data
RUN chmod a+x /start.sh
ENTRYPOINT ["/start.sh"]
CMD ["/start.sh"]