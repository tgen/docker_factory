ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libpng-1.6.37.tar.xz || \
                       wget -t 3 https://downloads.sourceforge.net/libpng/libpng-1.6.37.tar.xz && \
                       echo "505e70834d35383537b6491e7ae8641f1a4bed1876dbfe361201fc80868d88ca  libpng-1.6.37.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf  libpng-1.6.37.tar.xz && \
    cd /tmp/scratch/libpng-1.6.37 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     ./configure \
                                       --prefix=/opt && \
                                     make -j$(nproc) && \
                                     make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

ENV PATH /opt/bin/

