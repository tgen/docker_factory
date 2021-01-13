ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gzip-1.10.tar.xz || \
                       wget -t 3 https://ftp.gnu.org/gnu/gzip/gzip-1.10.tar.xz && \
                       echo "8425ccac99872d544d4310305f915f5ea81e04d0f437ef1a230dc9d1c819d7c0  gzip-1.10.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gzip-1.10.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../gzip-1.10/configure \
                             --prefix=/opt && \
    cd /tmp/scratch/build && make -j$(nproc) && \
                             make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

