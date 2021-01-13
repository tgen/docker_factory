ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 http://localhost:8000/sqlite-autoconf-3310100.tar.gz || \
                       wget -t 3 https://www.sqlite.org/2020/sqlite-autoconf-3310100.tar.gz && \
                       echo "62284efebc05a76f909c580ffa5c008a7d22a1287285d68b7825a2b6b51949ae  sqlite-autoconf-3310100.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf sqlite-autoconf-3310100.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ../sqlite-autoconf-3310100/configure --prefix=/opt && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

