ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/fastp-0.20.0.tar.gz || \
                       wget -t 3 https://github.com/OpenGene/fastp/archive/v0.20.0.tar.gz -O fastp-0.20.0.tar.gz && \
                       echo "8d751d2746db11ff233032fc49e3bcc8b53758dd4596fdcf4b4099a4d702ac22  fastp-0.20.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf fastp-0.20.0.tar.gz && \
    cd /tmp/scratch/fastp-0.20.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    PREFIX=/opt \
                                    make -j$(nproc) && \
                                    mkdir -p /opt/bin && \
                                    PREFIX=/opt \
                                    make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=opt_zlib /opt/lib/ /opt/lib/
COPY --from=build /opt /opt

