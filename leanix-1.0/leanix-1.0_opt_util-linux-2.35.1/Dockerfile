ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/util-linux-2.35.1.tar.xz || \
                       wget -t 3 https://cdn.kernel.org/pub/linux/utils/util-linux/v2.35/util-linux-2.35.1.tar.xz && \
                       echo "d9de3edd287366cd908e77677514b9387b22bc7b88f45b83e1922c3597f1d7f9  util-linux-2.35.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf util-linux-2.35.1.tar.xz && \
    cd /tmp/scratch/util-linux-2.35.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         ./configure \
                                           --prefix=/opt && \
                                         make -j$(nproc) && \
                                         make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

