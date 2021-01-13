ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_freetype-2.10.2:${IMAGE_ARCH} AS opt_freetype
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_util-linux-2.35.1:${IMAGE_ARCH} AS opt_util-linux
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_expat-2.2.9:${IMAGE_ARCH} AS opt_expat
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gperf-3.1:${IMAGE_ARCH} AS opt_gperf

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_freetype /opt /opt
COPY --from=opt_util-linux /opt /opt
COPY --from=opt_expat /opt /opt
COPY --from=opt_gperf /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/fontconfig-2.13.1.tar.bz2 || \
                       wget -t 3 https://www.freedesktop.org/software/fontconfig/release/fontconfig-2.13.1.tar.bz2 && \
                       echo "f655dd2a986d7aa97e052261b36aa67b0a64989496361eca8d604e6414006741  fontconfig-2.13.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xf fontconfig-2.13.1.tar.bz2 && \
    cd /tmp/scratch/fontconfig-2.13.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         ./configure \
                                           --prefix=/opt \
                                           --disable-docs && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
