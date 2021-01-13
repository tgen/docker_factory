ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gzip-1.10:${IMAGE_ARCH} AS opt_gzip

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_zlib /opt /opt
COPY --from=opt_gzip /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/star-2.7.3a.tar.gz || \
                       wget -t 3 https://github.com/alexdobin/STAR/archive/2.7.3a.tar.gz -O star-2.7.3a.tar.gz && \
                       echo "de204175351dc5f5ecc40cf458f224617654bdb8e00df55f0bb03a5727bf26f9  star-2.7.3a.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf star-2.7.3a.tar.gz && \
    cd /tmp/scratch/STAR-2.7.3a/source/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" make -j$(nproc) STAR && \
    cd /tmp/scratch/STAR-2.7.3a/source/ && mkdir -p /opt/bin/ && cp STAR /opt/bin/ && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

