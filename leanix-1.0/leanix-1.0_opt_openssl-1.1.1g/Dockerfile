ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/openssl-1.1.1g.tar.gz || \
                       wget -t 3 https://www.openssl.org/source/openssl-1.1.1g.tar.gz && \
                       echo "ddb04774f1e32f0c49751e21b67216ac87852ceb056b75209af2443400636d46  openssl-1.1.1g.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf openssl-1.1.1g.tar.gz && \
    cd /tmp/scratch/openssl-1.1.1g && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ./config --prefix=/opt shared && \
                                      make -j$(nproc) && make -j$(nproc) install_sw && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
COPY --from=build /tools/ssl/certs/ /opt/ssl/certs/

