ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_curl /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/vt-0.57721.tar.gz || \
                       wget -t 3 https://github.com/atks/vt/archive/0.57721.tar.gz -O vt-0.57721.tar.gz && \
                       echo "8f06d464ec5458539cfa30f81a034f47fe7f801146fe8ca80c14a3816b704e17  vt-0.57721.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf vt-0.57721.tar.gz && \
    cd /tmp/scratch/vt-0.57721/ && OPTFLAG="-O3 -march=${IMAGE_ARCH}" \
                                   make -j$(nproc) && \
                                   mkdir -p /opt/bin && mv vt /opt/bin/ && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

