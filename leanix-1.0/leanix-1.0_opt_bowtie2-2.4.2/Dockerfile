ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_tbb-2021:${IMAGE_ARCH} AS opt_tbb
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_tbb /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bowtie2-2.4.2.tar.gz || \
                       wget -t 3 https://github.com/BenLangmead/bowtie2/archive/v2.4.2.tar.gz -O bowtie2-2.4.2.tar.gz && \
                       echo "ea33a1562faf759b21b3a905e20b87a3524ac4e53af8cd723d9a9f31ee159c8a  bowtie2-2.4.2.tar.gz" | sha256sum -c && \
    mkdir -p /tmp/scratch/ && tar -xvf bowtie2-2.4.2.tar.gz && \
    cd /tmp/scratch/bowtie2-2.4.2/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      cmake -DCMAKE_INSTALL_PREFIX=/opt/ \
                                      -DCMAKE_BUILD_TYPE=Release \
                                      -G "Ninja" \
                                      ../bowtie2-2.4.2/ && \
                                      ninja && \
                                      ninja install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

