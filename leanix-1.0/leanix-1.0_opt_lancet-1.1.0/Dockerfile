ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gmp-6.2.0:${IMAGE_ARCH} AS opt_gmp
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt
COPY --from=opt_curl /opt/ /opt/

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/ && \
    cd /tmp/scratch/ && git clone https://github.com/nygenome/lancet.git && \
    #cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/lancet-1.1.0.tar.gz || \
    #                   wget -t 3 https://github.com/nygenome/lancet/archive/v1.1.0.tar.gz -O lancet-1.1.0.tar.gz && \
    #                   echo "34ab90d61e5addb2b2f0d7b6b2ebe5de9ef9196475f01cf946b2ec9141a9448d  lancet-1.1.0.tar.gz" | sha256sum -c && \
    #cd /tmp/scratch && tar -xvf lancet-1.1.0.tar.gz && \
    cd /tmp/scratch/lancet/ && git branch 1.1.x && \
    cd /tmp/scratch/lancet/ && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                               CXXFLAGS="-O3 -march=${IMAGE_ARCH} -L/opt/lib/" \
                               make -j$(nproc) && \
    cd /tmp/scratch/lancet/src && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                                  make -j$(nproc) && \
                                  cp lancet /opt/bin/ && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt/bin /opt/bin
COPY --from=build /opt/lib /opt/lib

