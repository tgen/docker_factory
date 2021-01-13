ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bzip2-1.0.8.tar.gz || \
                       wget -t 3 https://sourceware.org/pub/bzip2/bzip2-1.0.8.tar.gz && \
                       echo "ab5a03176ee106d3f0fa90e381da478ddae405918153cca248e682cd0c4a2269  bzip2-1.0.8.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bzip2-1.0.8.tar.gz && \
    cd /tmp/scratch/bzip2-1.0.8/ && sed -i 's@\(ln -s -f \)$(PREFIX)/bin/@\1@' Makefile && \
    cd /tmp/scratch/bzip2-1.0.8/ && sed -i 's@ -O2 @ -O3 -march=${IMAGE_ARCH} @' Makefile && \
    cd /tmp/scratch/bzip2-1.0.8/ && make -f Makefile-libbz2_so && \
                                    make clean && make && make PREFIX=/opt install && \
                                    cp -v bzip2-shared /opt/bin/bzip2 && \
                                    cp -av libbz2.so* /opt/lib && \
                                    ln -s libbz2.so.1.0 /opt/lib/libbz2.so && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

