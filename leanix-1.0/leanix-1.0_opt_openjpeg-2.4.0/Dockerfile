ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/openjpeg-2.4.0.tar.gz || \
                        wget -t 3 https://github.com/uclouvain/openjpeg/archive/v2.4.0/openjpeg-2.4.0.tar.gz && \
                        echo "8702ba68b442657f11aaeb2b338443ca8d5fb95b0d845757968a7be31ef7f16d  openjpeg-2.4.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf openjpeg-2.4.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake ../openjpeg-2.4.0 \
                               -DCMAKE_INSTALL_PREFIX=/opt \
                               -DCMAKE_BUILD_TYPE=Release \
                               -DBUILD_STATIC_LIBS=OFF \
                               -G "Ninja" && \
    cd /tmp/scratch/build/ && ninja && \
                              ninja install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

