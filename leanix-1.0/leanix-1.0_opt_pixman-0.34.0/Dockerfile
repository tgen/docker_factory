ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/pixman-0.34.0.tar.gz || \
                       wget -t 3 https://www.cairographics.org/releases/pixman-0.34.0.tar.gz && \
                       echo "21b6b249b51c6800dc9553b65106e1e37d0e25df942c90531d4c3997aa20a88e  pixman-0.34.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf pixman-0.34.0.tar.gz && \
    cd /tmp/scratch/pixman-0.34.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     ./configure \
                                       --disable-static \
                                       --prefix=/opt && \
                                     make -j$(nproc) && \
                                     make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

ENV PATH /opt/bin/

