ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/liblbfgs-1.10.tar.gz || \
                         wget -t 3 https://src.fedoraproject.org/repo/pkgs/liblbfgs/liblbfgs-1.10.tar.gz/2a46da6c4014d6b1e8a8790a93edffbb/liblbfgs-1.10.tar.gz ) && \
                       echo "4158ab7402b573e5c69d5f6b03c973047a91e16ca5737d3347e3af9c906868cf  liblbfgs-1.10.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf liblbfgs-1.10.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../liblbfgs-1.10/configure --prefix=/opt && \
                             make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch/build

FROM scratch

COPY --from=build /opt /opt

ENV PATH /opt/bin/

