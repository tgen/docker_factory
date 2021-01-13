ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/fribidi-1.0.9.tar.xz || \
                       wget -t 3 https://github.com/fribidi/fribidi/releases/download/v1.0.9/fribidi-1.0.9.tar.xz && \
                       echo "c5e47ea9026fb60da1944da9888b4e0a18854a0e2410bbfe7ad90a054d36e0c7  fribidi-1.0.9.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf fribidi-1.0.9.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             meson --prefix=/opt ../fribidi-1.0.9 && \
                             ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

