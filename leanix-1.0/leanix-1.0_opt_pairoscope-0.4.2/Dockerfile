ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_freetype-2.10.2:${IMAGE_ARCH} AS opt_freetype
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_cairo-1.16.0:${IMAGE_ARCH} AS opt_cairo
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_ncursesw-6.2:${IMAGE_ARCH} AS opt_ncursesw
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

COPY --from=opt_freetype /opt /opt
COPY --from=opt_cairo /opt /opt
COPY --from=opt_ncursesw /opt /opt
COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt

RUN rm /tools/lib/libfreetype.so && \
    rm -rf /tools/include/freetype2

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/pairoscope-0.4.2.tar.gz || \
                         wget -t 3 https://github.com/genome/pairoscope/archive/v0.4.2.tar.gz -O pairoscope-0.4.2.tar.gz ) && \
                       echo "119525f1c8101005ed78e58dfc046357c03c2ea1d6171cffa2050c72200af7e7  pairoscope-0.4.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf pairoscope-0.4.2.tar.gz && \
    ln -s /opt/lib/libncurses.so /tools/lib/libcurses.so && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                             LDFLAGS="-L/opt/lib/" \
                             cmake -DCMAKE_INSTALL_PREFIX=/opt \
                                   -DCMAKE_BUILD_TYPE=Release \
                                   ../pairoscope-0.4.2/ && \
                             make && \
                             make install && \
    mv /usr/bin/pairoscope /opt/bin/ && \
    cd / && rm -rf /tmp/scratch/build

FROM scratch

COPY --from=build /opt /opt

