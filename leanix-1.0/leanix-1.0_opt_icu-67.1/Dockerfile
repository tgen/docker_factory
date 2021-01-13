ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/icu4c-67_1-src.tgz || \
                       wget -t 3 http://github.com/unicode-org/icu/releases/download/release-67-1/icu4c-67_1-src.tgz && \
                       echo "94a80cd6f251a53bd2a997f6f1b5ac6653fe791dfab66e1eb0227740fb86d5dc  icu4c-67_1-src.tgz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf icu4c-67_1-src.tgz && \
    cd /tmp/scratch/icu/source && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
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

