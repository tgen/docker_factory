ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_readline-8.0:${IMAGE_ARCH} AS opt_readline

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_readline /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/gawk-5.0.1.tar.xz || \
                       wget -t 3 http://ftp.gnu.org/gnu/gawk/gawk-5.0.1.tar.xz && \
                       echo "8e4e86f04ed789648b66f757329743a0d6dfb5294c3b91b756a474f1ce05a794  gawk-5.0.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gawk-5.0.1.tar.xz && \
    cd /tmp/scratch/gawk-5.0.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  ./configure \
                                    --prefix=/opt && \
                                  make -j$(nproc) && \
                                  make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

