ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openssl-1.1.1g:${IMAGE_ARCH} AS opt_openssl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_pcre-8.44:${IMAGE_ARCH} AS opt_pcre
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_openssl /opt /opt
COPY --from=opt_pcre /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:/opt/share/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/wget-1.21.1.tar.gz || \
                       wget -t 3 https://ftp.gnu.org/gnu/wget/wget-1.21.1.tar.gz && \
                       echo "59ba0bdade9ad135eda581ae4e59a7a9f25e3a4bde6a5419632b31906120e26e  wget-1.21.1.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf wget-1.21.1.tar.gz && \
    cd /tmp/scratch/wget-1.21.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   ./configure --prefix=/opt --sysconfdir=/etc --with-ssl=openssl && \
                                   make -j$(nproc) && make -j$(nproc) install && \
    cp /tools/lib/libuuid.so.1 /opt/lib/ && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

