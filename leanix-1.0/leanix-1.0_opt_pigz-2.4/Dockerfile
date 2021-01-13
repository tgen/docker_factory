ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/pigz-2.4.tar.gz || \
                       wget -t 3 https://zlib.net/pigz/pigz-2.4.tar.gz && \
                       echo "a4f816222a7b4269bd232680590b579ccc72591f1bb5adafcd7208ca77e14f73  pigz-2.4.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf pigz-2.4.tar.gz && \
    cd /tmp/scratch/pigz-2.4 && sed -i 's|CFLAGS=|CFLAGS= -march=${IMAGE_ARCH} |g' Makefile && \
    cd /tmp/scratch/pigz-2.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                make -j$(nproc) && \
                                mkdir -p /opt/bin && cp pigz unpigz /opt/bin/ && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

