ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/swig-3.0.12.tar.gz || \
                       wget -t 3 http://prdownloads.sourceforge.net/swig/swig-3.0.12.tar.gz && \
                       echo "7cf9f447ae7ed1c51722efc45e7f14418d15d7a1e143ac9f09a668999f4fc94d  swig-3.0.12.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf swig-3.0.12.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../swig-3.0.12/configure \
                                  --prefix=/opt && \
                             make && \
                             make install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

