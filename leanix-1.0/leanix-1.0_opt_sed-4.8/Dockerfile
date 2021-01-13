ARG DOCKER_REGISTRY=127.0.0.1:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://127.0.0.1:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/sed-4.8.tar.xz || \
                       wget -t 3 ftp://ftp.gnu.org/gnu/sed/sed-4.8.tar.xz && \
                       echo "f79b0cfea71b37a8eeec8490db6c5f7ae7719c35587f21edb0617f370eeff633  sed-4.8.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf sed-4.8.tar.xz && \
    cd /tmp/scratch/sed-4.8 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ./configure \
                                  --prefix=/opt && \
                               make && \
                               make install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

