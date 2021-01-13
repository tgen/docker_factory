ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && git clone https://gitlab.com/libeigen/eigen.git && \
                        cd eigen && git checkout daf9bbeca26e98da2eed0058835cbb04e0a30ad8 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake \
                               -DCMAKE_INSTALL_PREFIX=/opt \
                               -DCMAKE_BUILD_TYPE=Release  \
                               -G "Ninja" \
                               ../eigen/ && \
    cd /tmp/scratch/build && ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

