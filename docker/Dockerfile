FROM ubuntu:18.04 as base

WORKDIR /build

ENV LC_ALL=C.UTF-8 \ 
    LANG=C.UTF-8

RUN apt-get update -qq && apt-get install -qqy --no-install-recommends software-properties-common
RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt-get update -qq && \
    apt-get install -qqy --no-install-recommends \
    python3.8 \
    python3-pip \
    build-essential \
    libssl-dev \
    libffi-dev \
    python3.8-dev \
    neovim \
    emacs \
    screen \
    git \
    wget \
    curl \
    cmake \
    htop 

# clean up. Need to change python3 to 3.8 or pip installs get confused
RUN rm -rf /var/lib/apt/lists/* && \
    rm -f /usr/bin/python3 && \
    rm -f /usr/bin/python3.6* && \
    rm -f /usr/bin/python3.7* && \
    rm -f /usr/bin/python && \
    ln -s /usr/bin/python3.8 /usr/bin/python3 && \
    ln -s /usr/bin/python3.8 /usr/bin/python && \
    ln -s /usr/bin/pip3 /usr/bin/pip && \
    rm -rf ~/.cache

## install pip libraries needed for most installs
RUN pip install wheel==0.35.1 && \
    pip install setuptools==50.3.0 && \
    pip install -U pip==20.2.3

WORKDIR /

RUN rm -rf /build

FROM base

COPY . /build

WORKDIR /build

# Separely split pip to avoid pip install main issue
RUN apt-get update &&\
    apt-get install -y libpq-dev &&\
    pip install -r requirements.txt 

# Plotly-orca depedencies
RUN apt-get install -y --no-install-recommends \
        xvfb \
        libgtk2.0-0 \
        libxtst6 \
        libxss1 \
        libgconf-2-4 \
        libnss3 \
        libasound2 && \
     mkdir -p /home/orca && \
     cd /home/orca && \
     wget https://github.com/plotly/orca/releases/download/v1.3.1/orca-1.3.1.AppImage && \
     chmod +x orca-1.3.1.AppImage && \
     ./orca-1.3.1.AppImage --appimage-extract && \
     printf '#!/bin/bash \nxvfb-run --auto-servernum --server-args "-screen 0 640x480x24" /home/orca/squashfs-root/app/orca "$@"' > /usr/bin/orca && \
     chmod +x /usr/bin/orca

ENV PYTHONPATH="/mnt:${PYTHONPATH}"

WORKDIR /mnt

RUN rm -rf /build










