ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/xz-5.2.5.tar.gz || \
                       wget -t 3 xz-5.2.5.tar.gz https://tukaani.org/xz/xz-5.2.5.tar.gz || true && \
                       echo "f6f4910fd033078738bd82bfba4f49219d03b17eb0794eb91efbae419f4aba10  xz-5.2.5.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf xz-5.2.5.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ../xz-5.2.5/configure --prefix=/opt && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

