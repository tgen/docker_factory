ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libxml2-2.9.10.tar.gz || \
                       wget -t 3 http://xmlsoft.org/sources/libxml2-2.9.10.tar.gz && \
                       echo "aafee193ffb8fe0c82d4afef6ef91972cbaf5feea100edc2f262750611b4be1f  libxml2-2.9.10.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxml2-2.9.10.tar.gz && \
    cd /tmp/scratch/libxml2-2.9.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                      --prefix=/opt && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

