ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gmp-6.2.0.tar.xz || \
                       wget -t 3 https://ftp.gnu.org/gnu/gmp/gmp-6.2.0.tar.xz && \
                       echo "258e6cd51b3fbdfc185c716d55f82c08aff57df0c6fbd143cf6ed561267a1526  gmp-6.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gmp-6.2.0.tar.xz && \
    cd /tmp/scratch/gmp-6.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ./configure --prefix=/opt && \
                                 make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

