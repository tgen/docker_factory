ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bwa-0.7.17.tar.bz2 || \
                       wget -t 3 https://github.com/lh3/bwa/releases/download/v0.7.17/bwa-0.7.17.tar.bz2 && \
                       echo "de1b4d4e745c0b7fc3e107b5155a51ac063011d33a5d82696331ecf4bed8d0fd  bwa-0.7.17.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bwa-0.7.17.tar.bz2 && \
    cd /tmp/scratch/bwa-0.7.17/ && sed -i 's| -O2| -O3 -march=${IMAGE_ARCH} -I/opt/include/|g' Makefile && \
    cd /tmp/scratch/bwa-0.7.17/ && sed -i 's|-lm|-L/opt/lib/ -lm|g' Makefile && \
    cd /tmp/scratch/bwa-0.7.17/ && sed -i 's|const uint8_t rle_auxtab|extern const uint8_t rle_auxtab|g' rle.h && \
    cd /tmp/scratch/bwa-0.7.17/ && make -j$(nproc) && \
                                   mkdir -p /opt/bin/ && \
                                   cp ./bwa /opt/bin/ && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
