# Copyright Stryd, Inc. February 2020, All rights reserved.
ARG base_image=ubuntu:24.04

FROM ${base_image}
ENV DOCKER_BASE_IMAGE ${base_image:-unspecified}

# Install all our dependencies, then blow away our list.
# apt-get has a stable CLI, but apt does not.  Prefer apt-get.
# ca-certificates is to support wget
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        wget ca-certificates \
        qemu-system-arm \
        gcc g++ \
        make \
        jq \
        python3 \
        python3-pip \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Get the nrfutil tooling
RUN wget https://developer.nordicsemi.com/.pc-tools/nrfutil/x64-linux/nrfutil -q -O /usr/local/bin/nrfutil && \
    chmod +x /usr/local/bin/nrfutil && \
    nrfutil install toolchain-manager=0.15.0

# Install the toolchain we need
ARG NCS_VERSION=v2.6.1
RUN nrfutil toolchain-manager install --ncs-version ${NCS_VERSION} && \
    nrfutil toolchain-manager env --as-script > /opt/toolchain-env.sh && \
    rm -rf /opt/ncs/downloads /root/ncs/downloads
    
# Setup our entrypoint
COPY ./entrypoint.sh /opt/image/entrypoint
ENV PATH="/opt/image:${PATH}"

LABEL description="The Virga Build System - arm-zephyr-eabi compatible build suite" \
      version="1.0" \
      image.base="${base_image}"
