ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libmaus2-2.0.780:${IMAGE_ARCH} AS opt_libmaus2

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_libmaus2 /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/biobambam2-2.0.182-release-20210412001032.tar.gz || \
                         wget -t 3 https://gitlab.com/german.tischler/biobambam2/-/archive/2.0.182-release-20210412001032/biobambam2-2.0.182-release-20210412001032.tar.gz ) && \
                       echo "47e46191b68c8a4d7ce6bb7614cfc47d2b682d06bd126698e065a8d5a33eaa59  biobambam2-2.0.182-release-20210412001032.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf biobambam2-2.0.182-release-20210412001032.tar.gz && \
    cd /tmp/scratch/biobambam2-2.0.182-release-20210412001032 && \
        CFLAGS="-O3 -march=${IMAGE_ARCH}" \
        CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
        ./configure \
          --prefix=/opt \
          --with-gmp=/opt && \
        make -j$(nproc) && \
        make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
