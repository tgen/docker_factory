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
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bwa-mem2-2.2.1.tar.gz || \
                       wget -t 3 https://github.com/bwa-mem2/bwa-mem2/releases/download/v2.2.1/Source_code_including_submodules.tar.gz -O bwa-mem2-2.2.1.tar.gz && \
                       echo "9b001bdc7666ee3f14f3698b21673714d429af50438b894313b05bc4688b1f6d  bwa-mem2-2.2.1.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bwa-mem2-2.2.1.tar.gz && \
    cd /tmp/scratch/bwa-mem2-2.2.1/ && make -j$(nproc) && \
                                       mkdir -p /opt/bin/ && \
                                       cp ./bwa-mem2 /opt/bin/ && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
