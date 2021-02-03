ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_cairo-1.16.0:${IMAGE_ARCH} AS opt_cairo
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_fribidi-1.0.9:${IMAGE_ARCH} AS opt_fribidi
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_icu-67.1:${IMAGE_ARCH} AS opt_icu
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_harfbuzz-2.7.2:${IMAGE_ARCH} AS opt_harfbuzz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libthai-0.1.28:${IMAGE_ARCH} AS opt_libthai
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_flex-2.6.4:${IMAGE_ARCH} AS opt_flex

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_cairo /opt /opt
COPY --from=opt_fribidi /opt /opt
COPY --from=opt_icu /opt /opt
#COPY --from=opt_harfbuzz /opt /opt
COPY --from=opt_libthai /opt /opt
COPY --from=opt_flex /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/share/pkgconfig/:/opt/lib/pkgconfig/:/opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gobject-introspection-1.64.1.tar.xz || \
                       wget -t 3 http://ftp.gnome.org/pub/gnome/sources/gobject-introspection/1.64/gobject-introspection-1.64.1.tar.xz && \
                       echo "80beae6728c134521926affff9b2e97125749b38d38744dc901f4010ee3e7fa7  gobject-introspection-1.64.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gobject-introspection-1.64.1.tar.xz && \
    cd /tmp/scratch/build && \
                             CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             meson \
                               --prefix=/opt \
                                 ../gobject-introspection-1.64.1 && \
                             ninja && \
                             ninja install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/harfbuzz-2.7.2.tar.xz || \
                       wget -t 3 https://github.com/harfbuzz/harfbuzz/releases/download/2.7.2/harfbuzz-2.7.2.tar.xz && \
                       echo "b8c048d7c2964a12f2c80deb6634dfc836b603dd12bf0d0a3df1627698e220ce  harfbuzz-2.7.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf harfbuzz-2.7.2.tar.xz && \
    cd /tmp/scratch/harfbuzz-2.7.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/opt \
                                        --with-gobject && \
                                     make -j$(nproc) && \
                                     make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/pango-1.46.1.tar.xz || \
                       wget -t 3 https://download.gnome.org/sources/pango/1.46/pango-1.46.1.tar.xz && \
                       echo "fe516b10711bbb6fd75011d66dd08fabfce18f7931aed7415136d53c4aadf1c5  pango-1.46.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf pango-1.46.1.tar.xz && \
    cd /tmp/scratch/build && \
                             CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             meson \
                               --prefix=/opt \
                                 ../pango-1.46.1 && \
                             ninja && \
                             ninja install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
