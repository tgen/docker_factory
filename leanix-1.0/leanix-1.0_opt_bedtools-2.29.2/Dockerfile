ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bedtools-2.29.2.tar.gz  || \
                       wget -t 3 https://github.com/arq5x/bedtools2/archive/v2.29.2.tar.gz -O bedtools-2.29.2.tar.gz && \
                       echo "bc2f36b5d4fc9890c69f607d54da873032628462e88c545dd633d2c787a544a5  bedtools-2.29.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bedtools-2.29.2.tar.gz && \
    cd /tmp/scratch/bedtools2-2.29.2/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         prefix=/opt make -j$(nproc) && \
                                         prefix=/opt make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
