ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openssl-1.1.1g:${IMAGE_ARCH} AS opt_openssl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

COPY --from=opt_openssl /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/rsync-3.2.3.tar.gz || \
                       wget -t 3 https://download.samba.org/pub/rsync/rsync-3.2.3.tar.gz && \
                       echo "becc3c504ceea499f4167a260040ccf4d9f2ef9499ad5683c179a697146ce50e  rsync-3.2.3.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf rsync-3.2.3.tar.gz && \
    cd /tmp/scratch/rsync-3.2.3/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./configure \
                                    --disable-xxhash \
                                    --disable-zstd \
                                    --disable-lz4 \
                                    --prefix=/opt && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    rm -rf /opt/lib64/*.a /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

ENV PATH /opt/bin/

