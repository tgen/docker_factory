ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && git clone https://github.com/oneapi-src/oneTBB.git && \
    cd /tmp/scratch/oneTBB/ && git checkout onetbb_2021 && \
    cd /tmp/scratch/build/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                              CXXFLAGS="-O3 -march=${IMAGE_ARCH} -Wno-deprecated-copy -Wno-maybe-uninitialized" \
                              cmake -DCMAKE_INSTALL_PREFIX=/opt/ \
                              -DCMAKE_BUILD_TYPE=Release \
                              -G "Ninja" \
                              ../oneTBB/ && \
                              ninja && \
                              ninja install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

