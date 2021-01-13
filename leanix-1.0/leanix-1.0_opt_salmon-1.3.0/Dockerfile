ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_boost /opt/ /opt/
COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_curl /opt/ /opt/
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/salmon-1.3.0.tar.gz || \
                         wget -t 3 https://github.com/COMBINE-lab/salmon/archive/v1.3.0.tar.gz -O salmon-1.3.0.tar.gz ) && \
                       echo "c105be481630d57e7022bf870eb040857834303abff05fe0e971dda5ed6f0236  salmon-1.3.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf salmon-1.3.0.tar.gz && \
    cd /tmp/scratch/salmon-1.3.0/ && sed -i 's|#include <unordered_map>|#include <unordered_map>\n#include <string>|g' include/BAMUtils.hpp && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake -DCMAKE_INSTALL_PREFIX=/opt/ \
                                   -DCMAKE_BUILD_TYPE=Release \
                                   -DUSE_SHARED_LIBS=ON \
                                   ../salmon-1.3.0/ && \
                             make -j$(nproc) && make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt/bin /opt/bin
COPY --from=build /opt/lib /opt/lib

