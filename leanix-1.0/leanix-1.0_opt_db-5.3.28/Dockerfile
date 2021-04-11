ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/db-5.3.28.tar.gz || \
                       wget -t 3 https://ftp.tw.freebsd.org/distfiles/bdb/db-5.3.28.tar.gz && \
                       echo "e0a992d740709892e81f9d93f06daf305cf73fb81b545afe72478043172c3628  db-5.3.28.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf db-5.3.28.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../db-5.3.28/dist/configure --prefix=/opt --enable-compat185 --enable-cxx && \
    cd /tmp/scratch/db-5.3.28 && sed -i 's|__atomic_compare_exchange|__atomic_compare_exchange_db|g' src/dbinc/atomic.h && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

