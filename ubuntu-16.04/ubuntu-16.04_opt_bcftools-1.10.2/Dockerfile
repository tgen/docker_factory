ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/ubuntu-16.04_bootstrap:${IMAGE_ARCH} as build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN apt update -y && \
    apt install -y zlib1g-dev libbz2-dev liblzma-dev libcurl4-openssl-dev && \
    apt autoremove -y && \
    apt clean

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bcftools-1.10.2.tar.bz2 || \
                       wget -t 3 https://github.com/samtools/bcftools/releases/download/1.10.2/bcftools-1.10.2.tar.bz2 && \
                       echo "f57301869d0055ce3b8e26d8ad880c0c1989bf25eaec8ea5db99b60e31354e2c  bcftools-1.10.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf bcftools-1.10.2.tar.bz2 && \
    cd /tmp/scratch/bcftools-1.10.2/ && CFLAGS="-O3 -march=sandybridge -I/opt/include/" CXXFLAGS="-O3 -march=sandybridge -I/opt/include/" LDFLAGS="-L/opt/lib/" \
                                          ./configure \
                                            --prefix=/opt && \
                                        make -j$(nproc) && make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

