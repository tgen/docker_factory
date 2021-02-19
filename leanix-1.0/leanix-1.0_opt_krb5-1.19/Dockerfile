ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/krb5-1.19.tar.gz || \
                         wget -t 3 https://kerberos.org/dist/krb5/1.19/krb5-1.19.tar.gz ) && \
                       echo "bc7862dd1342c04e1c17c984a268d50f29c0a658a59a22bd308ffa007d532a2e  krb5-1.19.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf krb5-1.19.tar.gz && \
    cd /tmp/scratch/krb5-1.19/src && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     ./configure \
                                       --prefix=/opt && \
                                     make -j$(nproc) && \
                                     make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

