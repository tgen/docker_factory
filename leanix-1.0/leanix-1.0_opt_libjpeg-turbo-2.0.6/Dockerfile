ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/libjpeg-turbo-2.0.6.tar.gz || \
                        wget -t 3 https://downloads.sourceforge.net/libjpeg-turbo/libjpeg-turbo-2.0.6.tar.gz && \
                        echo "d74b92ac33b0e3657123ddcf6728788c90dc84dcb6a52013d758af3c4af481bb  libjpeg-turbo-2.0.6.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf libjpeg-turbo-2.0.6.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake ../libjpeg-turbo-2.0.6 \
                               -DCMAKE_INSTALL_PREFIX=/opt \
                               -DCMAKE_BUILD_TYPE=Release \
                               -G "Ninja" && \
    cd /tmp/scratch/build/ && ninja && \
                              ninja install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

