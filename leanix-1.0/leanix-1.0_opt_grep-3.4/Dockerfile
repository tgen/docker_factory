ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_pcre-8.44:${IMAGE_ARCH} AS opt_pcre

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_pcre /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/grep-3.4.tar.xz || \
                       wget -t 3 http://ftp.gnu.org/gnu/grep/grep-3.4.tar.xz && \
                       echo "58e6751c41a7c25bfc6e9363a41786cff3ba5709cf11d5ad903cf7cce31cc3fb  grep-3.4.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf grep-3.4.tar.xz && \
    cd /tmp/scratch/grep-3.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                ./configure \
                                  --prefix=/opt && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
