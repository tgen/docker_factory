ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gffread-0.12.6.tar.gz || \
                         wget -t 3 https://github.com/gpertea/gffread/releases/download/v0.12.6/gffread-0.12.6.tar.gz ) && \
                       echo "1c558151f668d6f6fa20be9abb42ad224889855681f6e816b0ad681d85a5ce1b  gffread-0.12.6.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gffread-0.12.6.tar.gz && \
    cd /tmp/scratch/gffread-0.12.6 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      make -j$(nproc) && \
                                      mkdir -p /opt/bin/ && \
                                      mv gffread /opt/bin/ && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

