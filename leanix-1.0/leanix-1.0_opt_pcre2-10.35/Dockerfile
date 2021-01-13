ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_readline-8.0:${IMAGE_ARCH} AS opt_readline
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_readline /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/pcre2-10.35.tar.bz2 || \
                        wget -t 3 https://ftp.pcre.org/pub/pcre/pcre2-10.35.tar.bz2 && \
                        echo "9ccba8e02b0ce78046cdfb52e5c177f0f445e421059e43becca4359c669d4613  pcre2-10.35.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf pcre2-10.35.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -mshstk -I/opt/include/" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             LDFLAGS="-L/opt/lib/" \
                               ../pcre2-10.35/configure \
                                --prefix=/opt \
                                --docdir=/opt/share/doc/pcre2-10.35 \
                                --enable-unicode                    \
                                --enable-jit                        \
                                --enable-pcre2-16                   \
                                --enable-pcre2-32                   \
                                --enable-pcre2grep-libz             \
                                --enable-pcre2grep-libbz2           \
                                --enable-pcre2test-libreadline      \
                                --disable-static && \
                             make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

