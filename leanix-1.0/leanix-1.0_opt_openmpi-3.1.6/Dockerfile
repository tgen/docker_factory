ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/openmpi-3.1.6.tar.gz || \
                         wget -t 3 https://download.open-mpi.org/release/open-mpi/v3.1/openmpi-3.1.6.tar.gz ) && \
                       echo "627d350846008dff233ff2acc8542d20000a6abc1daa7277244d060298f88001  openmpi-3.1.6.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf openmpi-3.1.6.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../openmpi-3.1.6/configure \
                               --prefix=/opt && \
                             make && \
                             make install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

