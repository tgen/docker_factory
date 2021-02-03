ARG DOCKER_REGISTRY=localhost:5000

#FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:sandybridge
FROM ubuntu:20.04 AS build

RUN apt update -y && \
    apt install -y apt-utils && \
    apt autoremove -y && \
    apt clean

ENV DEBIAN_FRONTEND noninteractive

RUN apt update -y && \
    apt install -y \
     bison \
     build-essential \
     ccache \
     cmake \
     file \
     gawk \
     gettext \
     git \
     linux-headers-generic \
     m4 \
     meson \
     ninja-build \
     pkg-config \
     python3-setuptools \
     subversion \
     texinfo \
     wget \
     && \
    apt autoremove -y && \
    apt clean

#RUN update-alternatives --install "/usr/bin/ld" "ld" "/usr/bin/ld.lld-10" 20 && \
#    update-alternatives --install "/usr/bin/ld" "ld" "/usr/bin/ld.bfd" 10 && \
#    update-alternatives --config ld