ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_python-3.8.6.headless:${IMAGE_ARCH} AS opt_python
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gmp-6.2.0:${IMAGE_ARCH} AS opt_gmp
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_python /opt/ /opt/
COPY --from=opt_boost /opt/ /opt/
COPY --from=opt_htslib /opt/ /opt/
COPY --from=opt_gmp /opt/ /opt/
COPY --from=opt_curl /opt/ /opt/

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/octopus-0.6.3-beta.tar.gz || \
                         wget -t 3 https://github.com/luntergroup/octopus/archive/v0.6.3-beta.tar.gz -O octopus-0.6.3-beta.tar.gz ) && \
                       echo "46e3d8ecc737e98a00aa66eefd663edb2b606873839b898210a4369311c12c67  octopus-0.6.3-beta.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf octopus-0.6.3-beta.tar.gz && \
    cd /tmp/scratch/octopus-0.6.3-beta/ && sed -i 's|-Werror|-Wno-error|g' src/CMakeLists.txt && \
    cd /tmp/scratch/octopus-0.6.3-beta/ && ./scripts/install.py \
                               --prefix /opt \
                               --threads 4 \
                               --gmp /opt \
                               --boost /opt \
                               --htslib /opt && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

#RUN mkdir -p /tmp/scratch/build && \
#    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/octopus-0.6.3-beta.tar.gz || \
#                         wget -t 3 https://github.com/luntergroup/octopus/archive/v0.6.3-beta.tar.gz -O octopus-0.6.3-beta.tar.gz ) && \
#                       echo "46e3d8ecc737e98a00aa66eefd663edb2b606873839b898210a4369311c12c67  octopus-0.6.3-beta.tar.gz" | sha256sum -c && \
#    cd /tmp/scratch && tar -xvf octopus-0.6.3-beta.tar.gz && \
#    cd /tmp/scratch/octopus-0.6.3-beta/ && sed -i 's|-Werror|-Wno-error|g' src/CMakeLists.txt && \
#    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
#                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
#                             cmake -DCMAKE_INSTALL_PREFIX=/opt/ \
#                                   -DCMAKE_BUILD_TYPE=Release \
#                                   ../octopus-0.6.3-beta/ && \
#                             make && make install && \
#    rm -rf /opt/lib/*.a && \
#    cd / && rm -rf /tmp/scratch

RUN mv /opt/octopus /opt/bin/octopus

FROM scratch

COPY --from=build /opt/bin /opt/bin
COPY --from=build /opt/lib /opt/lib

