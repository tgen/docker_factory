ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/boost_1_74_0.tar.bz2 || \
                       wget -t 3 https://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.tar.bz2 && \
                       echo "83bfc1507731a0906e387fc28b7ef5417d591429e51e788417fe9ff025e116b1  boost_1_74_0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xf boost_1_74_0.tar.bz2 && \
    cd /tmp/scratch/boost_1_74_0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./bootstrap.sh \
                                      --prefix=/opt && \
                                    ./b2 -j$(nproc) && \
                                    ./b2 -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

