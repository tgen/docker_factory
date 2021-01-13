ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libffi-3.3.tar.gz || \
                       wget -t 3 https://gcc.gnu.org/pub/libffi/libffi-3.3.tar.gz && \
                       echo "72fba7922703ddfa7a028d513ac15a85c8d54c8d67f55fa5a4802885dc652056  libffi-3.3.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf libffi-3.3.tar.gz && \
    cd /tmp/scratch/libffi-3.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  ./configure \
                                    --prefix=/opt \
                                    --enable-shared && \
                                  make -j$(nproc) && \
                                  make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

