ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 http://localhost:8000/Miniconda2-4.7.12.1-Linux-x86_64.sh || \
                        wget -t 3 https://repo.anaconda.com/miniconda/Miniconda2-4.7.12.1-Linux-x86_64.sh && \
                        echo "383fe7b6c2574e425eee3c65533a5101e68a2d525e66356844a80aa02a556695  Miniconda2-4.7.12.1-Linux-x86_64.sh" | sha256sum -c && \
    cd /tmp/scratch/ && bash ./Miniconda2-4.7.12.1-Linux-x86_64.sh -p /opt/miniconda -b && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

