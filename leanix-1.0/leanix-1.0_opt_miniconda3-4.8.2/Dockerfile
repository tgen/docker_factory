ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/Miniconda3-py38_4.8.2-Linux-x86_64.sh || \
                        wget -t 3 https://repo.continuum.io/miniconda/Miniconda3-py38_4.8.2-Linux-x86_64.sh && \
                        echo "5bbb193fd201ebe25f4aeb3c58ba83feced6a25982ef4afa86d5506c3656c142  Miniconda3-py38_4.8.2-Linux-x86_64.sh" | sha256sum -c && \
    cd /tmp/scratch/ && bash ./Miniconda3-py38_4.8.2-Linux-x86_64.sh -p /opt/miniconda -b -f && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

