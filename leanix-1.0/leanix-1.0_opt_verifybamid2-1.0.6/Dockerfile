ARG SOURCE_DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt/ /opt/
COPY --from=opt_curl /opt/ /opt/
COPY --from=opt_htslib /opt/ /opt/
COPY --from=opt_xz /opt/ /opt/
COPY --from=opt_zlib /opt/ /opt/

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/VerifyBamID-1.0.6.tar.gz || \
                        wget -t 3 https://github.com/Griffan/VerifyBamID/archive/1.0.6.tar.gz -O VerifyBamID-1.0.6.tar.gz && \
                        echo "89fad013d8e47f27e6a33a2a977074f9aa9022650d79d0aa3d75694533e9c1d2  VerifyBamID-1.0.6.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf VerifyBamID-1.0.6.tar.gz && \
    cd /tmp/scratch/VerifyBamID-1.0.6/ && mkdir build && cd build/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                                                        cmake ../ -G "Ninja" -DCMAKE_INSTALL_PREFIX=/opt && \
                                                                      ninja && cp ../bin/VerifyBamID /opt/bin/ && \
    cd /tmp/scratch/VerifyBamID-1.0.6/ && cp -r resource /opt/ && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

