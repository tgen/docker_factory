ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000


FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libffi-3.3:${IMAGE_ARCH} AS opt_libffi
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_ncursesw-6.2:${IMAGE_ARCH} AS opt_ncursesw
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openssl-1.1.1g:${IMAGE_ARCH} AS opt_openssl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_readline-8.0:${IMAGE_ARCH} AS opt_readline
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_sqlite-3.31.1:${IMAGE_ARCH} AS opt_sqlite
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_libffi /opt /opt
COPY --from=opt_ncursesw /opt /opt
COPY --from=opt_openssl /opt /opt
COPY --from=opt_readline /opt /opt
COPY --from=opt_sqlite /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/Python-3.8.6.tar.xz || \
                       wget -t 3 https://www.python.org/ftp/python/3.8.6/Python-3.8.6.tar.xz && \
                       echo "a9e0b79d27aa056eb9cce8d63a427b5f9bab1465dee3f942dcfdb25a82f4ab8a  Python-3.8.6.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf Python-3.8.6.tar.xz && \
    cd /tmp/scratch/Python-3.8.6/ && sed -i "s/Werror=implicit-function-declaration/Wno-error/g" configure && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/ncursesw/ -I/opt/include/" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             LDFLAGS="-L/opt/lib64/" \
                             ../Python-3.8.6/configure \
                               --prefix=/opt \
                               --with-openssl=/opt \
                               --enable-shared && \
    cd /tmp/scratch/build && make -j$(nproc) && \
                             make -j$(nproc) install && \
    ln -s python3 /opt/bin/python && \
    python -m pip install --force-reinstall pip && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

