ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/expat-2.2.9.tar.xz || \
                       wget -t 3 https://prdownloads.sourceforge.net/expat/expat-2.2.9.tar.xz && \
                       echo "1ea6965b15c2106b6bbe883397271c80dfa0331cdf821b2c319591b55eadc0a4  expat-2.2.9.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf expat-2.2.9.tar.xz && \
    cd /tmp/scratch/expat-2.2.9 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ./configure --prefix=/opt && \
                                     make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

