ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libxml2-2.9.10:${IMAGE_ARCH} AS opt_libxml2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_freetype-2.10.2:${IMAGE_ARCH} AS opt_freetype
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_dbus-1.12.16:${IMAGE_ARCH} AS opt_dbus
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_glib-2.64.3:${IMAGE_ARCH} AS opt_glib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_libxml2 /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:/opt/share/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/util-macros-1.19.2.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/util/util-macros-1.19.2.tar.bz2 && \
                       echo "d7e43376ad220411499a79735020f9d145fdc159284867e99467e0d771f3e712  util-macros-1.19.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf util-macros-1.19.2.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                            ../util-macros-1.19.2/configure --prefix=/opt \
                                            --sysconfdir=/opt \
                                            --localstatedir=/var \
                                             && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch
    
RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xorgproto-2020.1.tar.bz2 || \
                       wget -t 3 https://xorg.freedesktop.org/archive/individual/proto/xorgproto-2020.1.tar.bz2 && \
                       echo "54a153f139035a376c075845dd058049177212da94d7a9707cf9468367b699d2  xorgproto-2020.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xorgproto-2020.1.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                        meson --prefix=/opt ../xorgproto-2020.1 && \
                             ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXau-1.0.9.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXau-1.0.9.tar.bz2 && \
                       echo "ccf8cbf0dbf676faa2ea0a6d64bcc3b6746064722b606c8c52917ed00dcb73ec  libXau-1.0.9.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXau-1.0.9.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../libXau-1.0.9/configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXdmcp-1.1.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXdmcp-1.1.3.tar.bz2 && \
                       echo "20523b44aaa513e17c009e873ad7bbc301507a3224c232610ce2e099011c6529  libXdmcp-1.1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXdmcp-1.1.3.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../libXdmcp-1.1.3/configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-proto-1.14.tar.xz || \
                       wget -t 3 https://xorg.freedesktop.org/archive/individual/proto/xcb-proto-1.14.tar.xz && \
                       echo "186a3ceb26f9b4a015f5a44dcc814c93033a5fc39684f36f1ecc79834416a605  xcb-proto-1.14.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-proto-1.14.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../xcb-proto-1.14/configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libxcb-1.14.tar.xz || \
                       wget -t 3 https://xorg.freedesktop.org/archive/individual/lib/libxcb-1.14.tar.xz && \
                       echo "a55ed6db98d43469801262d81dc2572ed124edc3db31059d4e9916eb9f844c34  libxcb-1.14.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxcb-1.14.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CPPFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../libxcb-1.14/configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xtrans-1.4.0.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/xtrans-1.4.0.tar.bz2 && \
                       echo "377c4491593c417946efcd2c7600d1e62639f7a8bbca391887e2c4679807d773  xtrans-1.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xtrans-1.4.0.tar.bz2 && \
    cd /tmp/scratch/xtrans-1.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libX11-1.6.9.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libX11-1.6.9.tar.bz2 && \
                       echo "9cc7e8d000d6193fa5af580d50d689380b8287052270f5bb26a5fb6b58b2bed1  libX11-1.6.9.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libX11-1.6.9.tar.bz2 && \
    cd /tmp/scratch/libX11-1.6.9 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXext-1.3.4.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXext-1.3.4.tar.bz2 && \
                       echo "59ad6fcce98deaecc14d39a672cf218ca37aba617c9a0f691cac3bcd28edf82b  libXext-1.3.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXext-1.3.4.tar.bz2 && \
    cd /tmp/scratch/libXext-1.3.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libFS-1.0.8.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libFS-1.0.8.tar.bz2 && \
                       echo "c8e13727149b2ddfe40912027459b2522042e3844c5cd228c3300fe5eef6bd0f  libFS-1.0.8.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libFS-1.0.8.tar.bz2 && \
    cd /tmp/scratch/libFS-1.0.8 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libICE-1.0.10.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libICE-1.0.10.tar.bz2 && \
                       echo "6f86dce12cf4bcaf5c37dddd8b1b64ed2ddf1ef7b218f22b9942595fb747c348  libICE-1.0.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libICE-1.0.10.tar.bz2 && \
    cd /tmp/scratch/libICE-1.0.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libSM-1.2.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libSM-1.2.3.tar.bz2 && \
                       echo "2d264499dcb05f56438dee12a1b4b71d76736ce7ba7aa6efbf15ebb113769cbb  libSM-1.2.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libSM-1.2.3.tar.bz2 && \
    cd /tmp/scratch/libSM-1.2.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXScrnSaver-1.2.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXScrnSaver-1.2.3.tar.bz2 && \
                       echo "f917075a1b7b5a38d67a8b0238eaab14acd2557679835b154cf2bca576e89bf8  libXScrnSaver-1.2.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXScrnSaver-1.2.3.tar.bz2 && \
    cd /tmp/scratch/libXScrnSaver-1.2.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXt-1.2.0.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXt-1.2.0.tar.bz2 && \
                       echo "b31df531dabed9f4611fc8980bc51d7782967e2aff44c4105251a1acb5a77831  libXt-1.2.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXt-1.2.0.tar.bz2 && \
    cd /tmp/scratch/libXt-1.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXmu-1.1.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXmu-1.1.3.tar.bz2 && \
                       echo "9c343225e7c3dc0904f2122b562278da5fed639b1b5e880d25111561bac5b731  libXmu-1.1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXmu-1.1.3.tar.bz2 && \
    cd /tmp/scratch/libXmu-1.1.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gperf-3.1.tar.gz || \
                       wget -t 3 https://ftp.gnu.org/gnu/gperf/gperf-3.1.tar.gz && \
                       echo "588546b945bba4b70b6a3a616e80b4ab466e3f33024a352fc2198112cdbb3ae2  gperf-3.1.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf gperf-3.1.tar.gz && \
    cd /tmp/scratch/gperf-3.1 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gettext-0.20.2.tar.xz || \
                       wget -t 3 https://ftp.gnu.org/gnu/gettext/gettext-0.20.2.tar.xz && \
                       echo "b22b818e644c37f6e3d1643a1943c32c3a9bff726d601e53047d2682019ceaba  gettext-0.20.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf gettext-0.20.2.tar.xz && \
    cd /tmp/scratch/gettext-0.20.2 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

COPY --from=opt_freetype /opt /opt

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/util-linux-2.35.1.tar.xz || \
                       wget -t 3 https://www.kernel.org/pub/linux/utils/util-linux/v2.35/util-linux-2.35.1.tar.xz && \
                       echo "d9de3edd287366cd908e77677514b9387b22bc7b88f45b83e1922c3597f1d7f9  util-linux-2.35.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf util-linux-2.35.1.tar.xz && \
    cd /tmp/scratch/util-linux-2.35.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                       --prefix=/opt/ \
                                       --disable-pylibmount \
                                       --disable-static     \
                                       --without-python     \
                                       --without-systemd    \
                                       --without-systemdsystemunitdir && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

#RUN whoami
#RUN chown root /
#RUN find / -name uuid && false

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/fontconfig-2.13.1.tar.bz2 || \
                       wget -t 3 https://www.freedesktop.org/software/fontconfig/release/fontconfig-2.13.1.tar.bz2 && \
                       echo "f655dd2a986d7aa97e052261b36aa67b0a64989496361eca8d604e6414006741  fontconfig-2.13.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf fontconfig-2.13.1.tar.bz2 && \
    cd /tmp/scratch/fontconfig-2.13.1 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      --disable-static \
                                      --disable-docs \
                                      --enable-libxml2 \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXpm-3.5.13.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXpm-3.5.13.tar.bz2 && \
                       echo "9cd1da57588b6cb71450eff2273ef6b657537a9ac4d02d0014228845b935ac25  libXpm-3.5.13.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXpm-3.5.13.tar.bz2 && \
    cd /tmp/scratch/libXpm-3.5.13 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXaw-1.0.13.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXaw-1.0.13.tar.bz2 && \
                       echo "8ef8067312571292ccc2bbe94c41109dcf022ea5a4ec71656a83d8cce9edb0cd  libXaw-1.0.13.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXaw-1.0.13.tar.bz2 && \
    cd /tmp/scratch/libXaw-1.0.13 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXfixes-5.0.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXfixes-5.0.3.tar.bz2 && \
                       echo "de1cd33aff226e08cefd0e6759341c2c8e8c9faf8ce9ac6ec38d43e287b22ad6  libXfixes-5.0.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXfixes-5.0.3.tar.bz2 && \
    cd /tmp/scratch/libXfixes-5.0.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXcomposite-0.4.5.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXcomposite-0.4.5.tar.bz2 && \
                       echo "b3218a2c15bab8035d16810df5b8251ffc7132ff3aa70651a1fba0bfe9634e8f  libXcomposite-0.4.5.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXcomposite-0.4.5.tar.bz2 && \
    cd /tmp/scratch/libXcomposite-0.4.5 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXrender-0.9.10.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXrender-0.9.10.tar.bz2 && \
                       echo "c06d5979f86e64cabbde57c223938db0b939dff49fdb5a793a1d3d0396650949  libXrender-0.9.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXrender-0.9.10.tar.bz2 && \
    cd /tmp/scratch/libXrender-0.9.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXcursor-1.2.0.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXcursor-1.2.0.tar.bz2 && \
                       echo "3ad3e9f8251094af6fe8cb4afcf63e28df504d46bfa5a5529db74a505d628782  libXcursor-1.2.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXcursor-1.2.0.tar.bz2 && \
    cd /tmp/scratch/libXcursor-1.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXdamage-1.1.5.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXdamage-1.1.5.tar.bz2 && \
                       echo "b734068643cac3b5f3d2c8279dd366b5bf28c7219d9e9d8717e1383995e0ea45  libXdamage-1.1.5.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXdamage-1.1.5.tar.bz2 && \
    cd /tmp/scratch/libXdamage-1.1.5 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libfontenc-1.1.4.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libfontenc-1.1.4.tar.bz2 && \
                       echo "2cfcce810ddd48f2e5dc658d28c1808e86dcf303eaff16728b9aa3dbc0092079  libfontenc-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libfontenc-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libfontenc-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXfont2-2.0.4.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXfont2-2.0.4.tar.bz2 && \
                       echo "6d151b3368e5035efede4b6264c0fdc6662c1c99dbc2de425e3480cababc69e6  libXfont2-2.0.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXfont2-2.0.4.tar.bz2 && \
    cd /tmp/scratch/libXfont2-2.0.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXft-2.3.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXft-2.3.3.tar.bz2 && \
                       echo "225c68e616dd29dbb27809e45e9eadf18e4d74c50be43020ef20015274529216  libXft-2.3.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXft-2.3.3.tar.bz2 && \
    cd /tmp/scratch/libXft-2.3.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXi-1.7.10.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXi-1.7.10.tar.bz2 && \
                       echo "36a30d8f6383a72e7ce060298b4b181fd298bc3a135c8e201b7ca847f5f81061  libXi-1.7.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXi-1.7.10.tar.bz2 && \
    cd /tmp/scratch/libXi-1.7.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXinerama-1.1.4.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXinerama-1.1.4.tar.bz2 && \
                       echo "0008dbd7ecf717e1e507eed1856ab0d9cf946d03201b85d5dcf61489bb02d720  libXinerama-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXinerama-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libXinerama-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXrandr-1.5.2.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXrandr-1.5.2.tar.bz2 && \
                       echo "8aea0ebe403d62330bb741ed595b53741acf45033d3bda1792f1d4cc3daee023  libXrandr-1.5.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXrandr-1.5.2.tar.bz2 && \
    cd /tmp/scratch/libXrandr-1.5.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXres-1.2.0.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXres-1.2.0.tar.bz2 && \
                       echo "ff75c1643488e64a7cfbced27486f0f944801319c84c18d3bd3da6bf28c812d4  libXres-1.2.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXres-1.2.0.tar.bz2 && \
    cd /tmp/scratch/libXres-1.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXtst-1.2.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXtst-1.2.3.tar.bz2 && \
                       echo "4655498a1b8e844e3d6f21f3b2c4e2b571effb5fd83199d428a6ba7ea4bf5204  libXtst-1.2.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXtst-1.2.3.tar.bz2 && \
    cd /tmp/scratch/libXtst-1.2.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXv-1.0.11.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXv-1.0.11.tar.bz2 && \
                       echo "d26c13eac99ac4504c532e8e76a1c8e4bd526471eb8a0a4ff2a88db60cb0b088  libXv-1.0.11.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXv-1.0.11.tar.bz2 && \
    cd /tmp/scratch/libXv-1.0.11 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXvMC-1.0.12.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXvMC-1.0.12.tar.bz2 && \
                       echo "6b3da7977b3f7eaf4f0ac6470ab1e562298d82c4e79077765787963ab7966dcd  libXvMC-1.0.12.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXvMC-1.0.12.tar.bz2 && \
    cd /tmp/scratch/libXvMC-1.0.12 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXxf86dga-1.1.5.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXxf86dga-1.1.5.tar.bz2 && \
                       echo "2b98bc5f506c6140d4eddd3990842d30f5dae733b64f198a504f07461bdb7203  libXxf86dga-1.1.5.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXxf86dga-1.1.5.tar.bz2 && \
    cd /tmp/scratch/libXxf86dga-1.1.5 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libXxf86vm-1.1.4.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libXxf86vm-1.1.4.tar.bz2 && \
                       echo "afee27f93c5f31c0ad582852c0fb36d50e4de7cd585fcf655e278a633d85cd57  libXxf86vm-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXxf86vm-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libXxf86vm-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libdmx-1.1.4.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libdmx-1.1.4.tar.bz2 && \
                       echo "253f90005d134fa7a209fbcbc5a3024335367c930adf0f3203e754cf32747243  libdmx-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libdmx-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libdmx-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libpciaccess-0.16.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libpciaccess-0.16.tar.bz2 && \
                       echo "214c9d0d884fdd7375ec8da8dcb91a8d3169f263294c9a90c575bf1938b9f489  libpciaccess-0.16.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libpciaccess-0.16.tar.bz2 && \
    cd /tmp/scratch/libpciaccess-0.16 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libxkbfile-1.1.0.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libxkbfile-1.1.0.tar.bz2 && \
                       echo "758dbdaa20add2db4902df0b1b7c936564b7376c02a0acd1f2a331bd334b38c7  libxkbfile-1.1.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxkbfile-1.1.0.tar.bz2 && \
    cd /tmp/scratch/libxkbfile-1.1.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libxshmfence-1.3.tar.bz2 || \
                       wget -t 3 https://www.x.org/pub/individual/lib/libxshmfence-1.3.tar.bz2 && \
                       echo "b884300d26a14961a076fbebc762a39831cb75f92bed5ccf9836345b459220c7  libxshmfence-1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxshmfence-1.3.tar.bz2 && \
    cd /tmp/scratch/libxshmfence-1.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/cups-2.3.3-source.tar.gz || \
                       wget -t 3 https://ftp.osuosl.org/pub/blfs/conglomeration/cups/cups-2.3.3-source.tar.gz && \
                       echo "261fd948bce8647b6d5cb2a1784f0c24cc52b5c4e827b71d726020bcc502f3ee  cups-2.3.3-source.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf cups-2.3.3-source.tar.gz && \
    cd /tmp/scratch/cups-2.3.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/alsa-lib-1.2.2.tar.bz2 || \
                       wget -t 3 https://www.alsa-project.org/files/pub/lib/alsa-lib-1.2.2.tar.bz2 && \
                       echo "d8e853d8805574777bbe40937812ad1419c9ea7210e176f0def3e6ed255ab3ec  alsa-lib-1.2.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf alsa-lib-1.2.2.tar.bz2 && \
    cd /tmp/scratch/alsa-lib-1.2.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-0.4.0.tar.bz2 || \
                       wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-0.4.0.tar.bz2 && \
                       echo "46e49469cb3b594af1d33176cd7565def2be3fa8be4371d62271fabb5eae50e9  xcb-util-0.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-0.4.0.tar.bz2 && \
    cd /tmp/scratch/xcb-util-0.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                           ./configure --prefix=/opt \
                                           --disable-static \
                                           && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-image-0.4.0.tar.bz2 || \
                       wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-image-0.4.0.tar.bz2 && \
                       echo "2db96a37d78831d643538dd1b595d7d712e04bdccf8896a5e18ce0f398ea2ffc  xcb-util-image-0.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-image-0.4.0.tar.bz2 && \
    cd /tmp/scratch/xcb-util-image-0.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                              ./configure --prefix=/opt \
                                              --disable-static \
                                            && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-keysyms-0.4.0.tar.bz2 || \
                       wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-keysyms-0.4.0.tar.bz2 && \
                       echo "0ef8490ff1dede52b7de533158547f8b454b241aa3e4dcca369507f66f216dd9  xcb-util-keysyms-0.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-keysyms-0.4.0.tar.bz2 && \
    cd /tmp/scratch/xcb-util-keysyms-0.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                                ./configure --prefix=/opt \
                                                --disable-static \
                                              && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-renderutil-0.3.9.tar.bz2 || \
                       wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-renderutil-0.3.9.tar.bz2 && \
                       echo "c6e97e48fb1286d6394dddb1c1732f00227c70bd1bedb7d1acabefdd340bea5b  xcb-util-renderutil-0.3.9.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-renderutil-0.3.9.tar.bz2 && \
    cd /tmp/scratch/xcb-util-renderutil-0.3.9 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                                   ./configure --prefix=/opt \
                                                   --disable-static \
                                                 && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-wm-0.4.1.tar.bz2 || \
                       wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-wm-0.4.1.tar.bz2 && \
                       echo "28bf8179640eaa89276d2b0f1ce4285103d136be6c98262b6151aaee1d3c2a3f  xcb-util-wm-0.4.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-wm-0.4.1.tar.bz2 && \
    cd /tmp/scratch/xcb-util-wm-0.4.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                           ./configure --prefix=/opt \
                                           --disable-static \
                                           && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-cursor-0.1.3.tar.bz2 || \
                       wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-cursor-0.1.3.tar.bz2 && \
                       echo "05a10a0706a1a789a078be297b5fb663f66a71fb7f7f1b99658264c35926394f  xcb-util-cursor-0.1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-cursor-0.1.3.tar.bz2 && \
    cd /tmp/scratch/xcb-util-cursor-0.1.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                               ./configure --prefix=/opt \
                                               --disable-static \
                                             && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xkeyboard-config-2.30.tar.bz2 || \
                       wget -t 3 https://www.x.org/archive//individual/data/xkeyboard-config/xkeyboard-config-2.30.tar.bz2 && \
                       echo "095a524f7b633ed257617202d06c9c71fe020c8897b106cf0dcdd0c6e8b797d4  xkeyboard-config-2.30.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xkeyboard-config-2.30.tar.bz2 && \
    cd /tmp/scratch/xkeyboard-config-2.30 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                               ./configure --prefix=/opt \
                                               --disable-static \
                                             && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch


RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libxkbcommon-0.10.0.tar.xz || \
                       wget -t 3 https://xkbcommon.org/download/libxkbcommon-0.10.0.tar.xz && \
                       echo "57c3630cdc38fb4734cd57fa349e92244f5ae3862813e533cedbd86721a0b6f2  libxkbcommon-0.10.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxkbcommon-0.10.0.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               meson --prefix=/opt -Denable-docs=false -Denable-wayland=false ../libxkbcommon-0.10.0 && \
                             ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libdrm-2.4.102.tar.xz || \
                       wget -t 3 https://dri.freedesktop.org/libdrm/libdrm-2.4.102.tar.xz && \
                       echo "8bcbf9336c28e393d76c1f16d7e79e394a7fce8a2e929d52d3ad7ad8525ba05b  libdrm-2.4.102.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libdrm-2.4.102.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               meson --prefix=/opt ../libdrm-2.4.102 && \
                             ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

COPY --from=opt_dbus /opt /opt
COPY --from=opt_glib /opt /opt

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/at-spi2-core-2.36.0.tar.xz || \
                       wget -t 3 http://ftp.gnome.org/pub/gnome/sources/at-spi2-core/2.36/at-spi2-core-2.36.0.tar.xz && \
                       echo "88da57de0a7e3c60bc341a974a80fdba091612db3547c410d6deab039ca5c05a  at-spi2-core-2.36.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf at-spi2-core-2.36.0.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               meson --prefix=/opt --sysconfdir=/opt -Dsystemd_user_dir=/tmp ../at-spi2-core-2.36.0 && \
                             ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

