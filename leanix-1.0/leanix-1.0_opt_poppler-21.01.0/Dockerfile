ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_cairo-1.16.0:${IMAGE_ARCH} AS opt_cairo
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openjpeg-2.4.0:${IMAGE_ARCH} AS opt_openjpeg
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libjpeg-turbo-2.0.6:${IMAGE_ARCH} AS opt_libjpeg-turbo

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_boost /opt /opt
COPY --from=opt_cairo /opt /opt
COPY --from=opt_openjpeg /opt /opt
COPY --from=opt_libjpeg-turbo /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/share/pkgconfig/:/opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/poppler-21.01.0.tar.xz || \
                        wget -t 3 https://ftp.osuosl.org/pub/blfs/conglomeration/poppler/poppler-21.01.0.tar.xz && \
                        echo "016dde34e5f868ea98a32ca99b643325a9682281500942b7113f4ec88d20e2f3  poppler-21.01.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf poppler-21.01.0.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake ../poppler-21.01.0 \
                               -DCMAKE_INSTALL_PREFIX=/opt \
                               -DCMAKE_BUILD_TYPE=Release \
                               -G "Ninja" && \
    cd /tmp/scratch/build/ && ninja && \
                              ninja install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

