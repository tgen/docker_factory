ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libffi-3.3:${IMAGE_ARCH} AS opt_libffi
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
COPY --from=opt_openssl /opt /opt
COPY --from=opt_readline /opt /opt
COPY --from=opt_sqlite /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/Python-2.7.18.tar.xz || \
                       wget -t 3 https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tar.xz && \
                       echo "b62c0e7937551d0cc02b8fd5cb0f544f9405bafc9a54d3808ed4594812edef43  Python-2.7.18.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf Python-2.7.18.tar.xz && \
    cd /tmp/scratch/Python-2.7.18/ && sed -i "s/Werror=implicit-function-declaration/Wno-error/g" configure && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/ncursesw/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ../Python-2.7.18/configure --prefix=/opt --with-openssl=/opt --enable-shared && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    cd /tmp/scratch/ && curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    cd /tmp/scratch/ && python get-pip.py && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

