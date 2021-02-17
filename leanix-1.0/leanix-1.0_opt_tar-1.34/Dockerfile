ARG DOCKER_REGISTRY=127.0.0.1:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://127.0.0.1:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/tar-1.34.tar.xz || \
                       wget -t 3 http://ftp.gnu.org/gnu/tar/tar-1.34.tar.xz && \
                       echo "63bebd26879c5e1eea4352f0d03c991f966aeb3ddeb3c7445c902568d5411d28  tar-1.34.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf tar-1.34.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ../tar-1.34/configure --prefix=/opt && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

