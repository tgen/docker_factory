ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_readline-8.0:${IMAGE_ARCH} AS opt_readline

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_readline /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/pcre-8.44.tar.gz || \
                       wget -t 3 https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz && \
                       echo "aecafd4af3bd0f3935721af77b889d9024b2e01d96b58471bd91a3063fb47728  pcre-8.44.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf pcre-8.44.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ../pcre-8.44/configure \
                                --prefix=/opt \
                                --enable-unicode-properties       \
                                --enable-pcre16                   \
                                --enable-pcre32                   \
                                --enable-pcregrep-libz            \
                                --enable-pcregrep-libbz2          \
                                --enable-pcretest-libreadline     \
                                --disable-static && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
