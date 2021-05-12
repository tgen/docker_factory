ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_ncursesw-6.2:${IMAGE_ARCH} AS opt_ncursesw

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt
COPY --from=opt_curl /opt /opt
COPY --from=opt_ncursesw /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/samtools-1.12.tar.bz2 || \
                       wget -t 3 https://github.com/samtools/samtools/releases/download/1.12/samtools-1.12.tar.bz2 && \
                       echo "6da3770563b1c545ca8bdf78cf535e6d1753d6383983c7929245d5dba2902dcb  samtools-1.12.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xf samtools-1.12.tar.bz2 && \
    cd /tmp/scratch/samtools-1.12/ && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" LDFLAGS="-L/opt/lib/" \
                                          ./configure \
                                            --prefix=/opt && \
                                        make -j$(nproc) && make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

