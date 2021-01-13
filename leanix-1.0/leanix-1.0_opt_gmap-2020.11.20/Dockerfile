ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gmap-gsnap-2020-11-20.tar.gz || \
                       wget -t 3 http://research-pub.gene.com/gmap/src/gmap-gsnap-2020-11-20.tar.gz && \
                       echo "4c45646c81cf03a4e39270e5f2d208bc85b14c847ca81f9e4154c21909050200  gmap-gsnap-2020-11-20.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf gmap-gsnap-2020-11-20.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../gmap-2020-11-20/configure --prefix=/opt && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

