ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gmp-6.2.0:${IMAGE_ARCH} AS opt_gmp
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_boost /opt/ /opt/
COPY --from=opt_htslib /opt/ /opt/
COPY --from=opt_gmp /opt/ /opt/
COPY --from=opt_curl /opt/ /opt/

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/octopus-0.7.0.tar.gz || \
                         wget -t 3 https://github.com/luntergroup/octopus/archive/v0.7.0.tar.gz -O octopus-0.7.0.tar.gz ) && \
                       echo "0b9d5689a6e02d58ecc7cb475a730fddd96ca506be2a047c588ed5a3e7d2d06b  octopus-0.7.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf octopus-0.7.0.tar.gz && \
    cd /tmp/scratch/octopus-0.7.0/ && sed -i 's|-Werror|-Wno-error|g' src/CMakeLists.txt && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake -DCMAKE_INSTALL_PREFIX=/opt/ \
                                   -DCMAKE_BUILD_TYPE=Release \
                                   ../octopus-0.7.0/ && \
                             make && make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt/bin /opt/bin
COPY --from=build /opt/lib /opt/lib

