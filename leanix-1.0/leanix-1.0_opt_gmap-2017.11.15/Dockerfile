ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gmap-gsnap-2017-11-15.tar.gz || \
                       wget -t 3 http://research-pub.gene.com/gmap/src/gmap-gsnap-2017-11-15.tar.gz && \
                       echo "3e1056d36dd03e834c4699482a7587f4ec9143ac8688320bc780f4df4cd83e13  gmap-gsnap-2017-11-15.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf gmap-gsnap-2017-11-15.tar.gz && \
    cd /tmp/scratch/gmap-2017-11-15/ && sed -i 's|bool homopolymerp;|static bool homopolymerp;|g' src/dynprog_end.c && \
    cd /tmp/scratch/gmap-2017-11-15/ && sed -i 's|bool homopolymerp;|static bool homopolymerp;|g' src/dynprog_single.c && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../gmap-2017-11-15/configure --prefix=/opt && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

