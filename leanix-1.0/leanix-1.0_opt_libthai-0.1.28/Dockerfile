ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libdatrie-0.2.12.tar.xz || \
                       wget -t 3 https://linux.thai.net/pub/thailinux/software/libthai/libdatrie-0.2.12.tar.xz && \
                       echo "452dcc4d3a96c01f80f7c291b42be11863cd1554ff78b93e110becce6e00b149  libdatrie-0.2.12.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf libdatrie-0.2.12.tar.xz && \
    cd /tmp/scratch/libdatrie-0.2.12 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                        CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                        ./configure \
                                          --prefix=/opt && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libthai-0.1.28.tar.xz || \
                       wget -t 3 https://linux.thai.net/pub/thailinux/software/libthai/libthai-0.1.28.tar.xz && \
                       echo "ffe0a17b4b5aa11b153c15986800eca19f6c93a4025ffa5cf2cab2dcdf1ae911  libthai-0.1.28.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf libthai-0.1.28.tar.xz && \
    cd /tmp/scratch/libthai-0.1.28 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/opt \
                                        --disable-dict && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
