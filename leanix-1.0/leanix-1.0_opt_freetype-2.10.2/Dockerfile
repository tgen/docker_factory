ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/freetype-2.10.2.tar.xz || \
                       wget -t 3 https://downloads.sourceforge.net/freetype/freetype-2.10.2.tar.xz && \
                       echo "1543d61025d2e6312e0a1c563652555f17378a204a61e99928c9fcef030a2d8b  freetype-2.10.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf freetype-2.10.2.tar.xz && \
    cd /tmp/scratch/freetype-2.10.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/opt \
                                      --sysconfdir=/opt \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

