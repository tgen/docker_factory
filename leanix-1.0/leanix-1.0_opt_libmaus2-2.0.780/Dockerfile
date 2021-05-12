ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gmp-6.2.0:${IMAGE_ARCH} AS opt_gmp
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_gmp /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/libmaus2-2.0.780-release-20210430155402.tar.gz || \
                       wget -t 3 https://gitlab.com/german.tischler/libmaus2/-/archive/2.0.780-release-20210430155402/libmaus2-2.0.780-release-20210430155402.tar.gz && \
                       echo "375c95f90cf14c2eaf97fbdd8de8e9f29ec89652ccba6361db57e8568f5b1121  libmaus2-2.0.780-release-20210430155402.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf libmaus2-2.0.780-release-20210430155402.tar.gz && \
    cd /tmp/scratch/libmaus2-2.0.780-release-20210430155402 && \
        CFLAGS="-O3 -march=${IMAGE_ARCH}" \
        CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
        ./configure \
          --prefix=/opt \
          --with-gmp=/opt \
          --enable-shared-libmaus2 \
          --enable-sse4 && \
        make -j$(nproc) && \
        make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

