ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib
#FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_rsync-3.2.3:${IMAGE_ARCH} AS opt_rsync

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

#COPY --from=opt_boost /opt/ /opt/
#COPY --from=opt_bzip2 /opt /opt
#COPY --from=opt_curl /opt/ /opt/
#COPY --from=opt_xz /opt /opt
#COPY --from=opt_zlib /opt /opt
#COPY --from=opt_rsync /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && git clone --recursive https://github.com/trinityrnaseq/trinityrnaseq.git && \
    cd /tmp/scratch/trinityrnaseq && git checkout v2.11.0
#RUN find /tmp/scratch -name sift_bam_max_cov.cpp && false
RUN cd /tmp/scratch/trinityrnaseq && \
        sed -i 's|#include "htslib/bgzf.h"|#include "htslib/bgzf.h"\n#include <string>|g' trinity-plugins/bamsifter/sift_bam_max_cov.cpp 
RUN cd /tmp/scratch/trinityrnaseq && mkdir CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                           CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                           make -j$(nproc) && make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt/bin /opt/bin
COPY --from=build /opt/lib /opt/lib

