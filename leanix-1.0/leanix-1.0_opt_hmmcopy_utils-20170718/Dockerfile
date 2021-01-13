ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_htslib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && ( wget -t 1 http://localhost:8000/hmmcopy_utils-20170718.tar.gz && tar -xvf hmmcopy_utils-20170718.tar.gz ) || \
                        ( git clone https://github.com/shahcompbio/hmmcopy_utils.git && cd hmmcopy_utils && git checkout 5911bf69f11d8d185f7054098d39799d2c6894cc ) && \
    mkdir -p /tmp/scratch/hmmcopy_utils/build/ && \
    cd /tmp/scratch/hmmcopy_utils/build/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                            CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                            cmake ../ \
                                             -G "Ninja" \
                                             -DCMAKE_INSTALL_PREFIX=/opt && \
                                            ninja && \
                                            mkdir -p /opt/bin/ && cp bin/* /opt/bin/ && \
    rm -rf /tools/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

