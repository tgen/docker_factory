ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gsl-2.6.tar.gz || \
                       wget -t 3 ftp://ftp.gnu.org/gnu/gsl/gsl-2.6.tar.gz && \
                       echo "b782339fc7a38fe17689cb39966c4d821236c28018b6593ddb6fd59ee40786a8  gsl-2.6.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gsl-2.6.tar.gz && \
    cd /tmp/scratch/gsl-2.6 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 ./configure \
                                   --prefix=/opt && \
                                 make -j$(nproc) && \
                                 make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

