ARG BUILD_FROM
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

WORKDIR /usr/src
ARG I2SMIC_ASANTERO_VERSION
ENV PIP_BREAK_SYSTEM_PACKAGES=1

RUN \
    echo "deb http://raspbian.raspberrypi.org/raspbian/ bullseye main contrib non-free rpi" >> /etc/apt/sources.list
    apt-get update \
    && apt-get install -y --no-install-recommends \
        python3 \
        python3-pip \
    \
    && pip3 install --upgrade adafruit-python-shell \
    && apt-get -y install git raspberrypi-kernel-headers \
    && git clone https://github.com/adafruit/Raspberry-Pi-Installer-Scripts.git \
    && cd Raspberry-Pi-Installer-Scripts/i2s_mic_module \
    && make clean \
    && make  \
    && make install \
    && echo "snd-i2smic-rpi" >> /etc/modules-load.d/snd-i2smic-rpi.conf \
    && echo "options snd-i2smic-rpi rpi_platform_generation=0" >> /etc/modprobe.d/snd-i2smic-rpi.conf \
    \
    && apt-get install -y --no-install-recommends \
       libportaudio0 \
       libportaudio2 \
       libportaudiocpp0 \ 
       portaudio19-dev \
    && pip3 install pyaudio \
       matplotlib \
       scipy \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY sounds/ ./sounds/

WORKDIR /
COPY rootfs /

HEALTHCHECK --start-period=10m \
    CMD echo '{ "type": "describe" }' \
        | nc -w 1 localhost 10800 \
        | grep -q "assist" \
        || exit 1
