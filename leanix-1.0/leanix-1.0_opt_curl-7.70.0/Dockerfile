ARG DOCKER_REGISTRY=127.0.0.1:5000
ARG IMAGE_ARCH=skylake
ARG LOCAL_SOURCES_SERVER=http://127.0.0.1:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openssl-1.1.1g:${IMAGE_ARCH} AS opt_openssl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_openssl /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/curl-7.70.0.tar.xz || \
                       wget -t 3 https://curl.haxx.se/download/curl-7.70.0.tar.xz && \
                       echo "032f43f2674008c761af19bf536374128c16241fb234699a55f9fb603fcfbae7  curl-7.70.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf curl-7.70.0.tar.xz && \
    mkdir -p /opt/ssl && cp -r /tools/ssl/certs /opt/ssl/ && \
    cd /tmp/scratch/curl-7.70.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   ./configure \
                                     --prefix=/opt \
                                     --disable-static \
                                     --with-ssl=/opt \
                                     --with-ca-bundle=/opt/ssl/certs/ca-certificates.crt && \
                                   make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

