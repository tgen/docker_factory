ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libpng-1.6.37:${IMAGE_ARCH} AS opt_libpng
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_pixman-0.34.0:${IMAGE_ARCH} AS opt_pixman
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_fontconfig-2.13.1:${IMAGE_ARCH} AS opt_fontconfig
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_glib-2.64.3:${IMAGE_ARCH} AS opt_glib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

COPY --from=opt_libpng /opt /opt
COPY --from=opt_pixman /opt /opt
COPY --from=opt_fontconfig /opt /opt
COPY --from=opt_glib /opt /opt

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/cairo-1.16.0.tar.xz || \
                       wget -t 3 https://cairographics.org/releases/cairo-1.16.0.tar.xz && \
                       echo "5e7b29b3f113ef870d1e3ecf8adf21f923396401604bda16d44be45e66052331  cairo-1.16.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf cairo-1.16.0.tar.xz && \
    cd /tmp/scratch/cairo-1.16.0 && png_REQUIRES="libpng" \
                                     CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/ -Wno-enum-conversion -Wno-int-in-bool-context -Wno-inline" \
                                     CXXFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/ -Wno-enum-conversion -Wnoint-in-bool-context -Wno-inline" \
                                     LDFLAGS="-L/opt/lib/ -lpng" \
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

