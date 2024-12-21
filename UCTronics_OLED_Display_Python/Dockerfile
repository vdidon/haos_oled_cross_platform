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
    xz-utils

RUN wget "https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-aarch64.tar.xz" && \
    tar Jxvf s6-overlay-aarch64.tar.xz -C / && \
    mkdir -p /etc/fix-attrs.d /etc/services.d

RUN wget -O /usr/bin/tempio "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_aarch64" && \
    chmod a+x /usr/bin/tempio && \
    mkdir -p /usr/src/bashio


    # && mkdir -p /usr/src/bashio \
    # && curl -L -f -s "https://github.com/hassio-addons/bashio/archive/v${BASHIO_VERSION}.tar.gz" \
    # | tar -xzf - --strip 1 -C /usr/src/bashio \
    # && mv /usr/src/bashio/lib /usr/lib/bashio \
    # && ln -s /usr/lib/bashio/bashio /usr/bin/bashio \
RUN mkdir -p /usr/src/bashio
RUN wget "https://github.com/hassio-addons/bashio/archive/v${BASHIO_VERSION}.tar.gz"
RUN tar -xzf v${BASHIO_VERSION}.tar.gz -C /usr/src/bashio --strip-components=1
# RUN mv /usr/src/bashio/lib /usr/lib/bashio
# RUN ln -s /usr/lib/bashio/bashio /usr/bin/bashio
RUN mv /usr/src/bashio/lib /usr/bin

RUN rm -rf /var/lib/apt/lists/* /usr/src/*

# Install Pythong Libs for this Add-on
RUN set -x && pip3 install requests \
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
CMD [ "/start.sh" ]