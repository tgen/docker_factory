ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/ncurses-6.2.tar.gz || \
                       wget -t 3 https://ftp.gnu.org/gnu/ncurses/ncurses-6.2.tar.gz && \
                       echo "30306e0c76e0f9f1f0de987cf1c82a5c21e1ce6568b9227f7da5b71cbea86c9d  ncurses-6.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf ncurses-6.2.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ../ncurses-6.2/configure \
                                --prefix=/opt     \
                                --with-shared     \
                                --without-debug   \
                                --without-normal  \
                                --enable-pc-files \
                                --enable-widec && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    cd /tmp/scratch/build && mkdir -p /opt/lib/pkgconfig/ && cp ./misc/*.pc /opt/lib/pkgconfig/ && \
    mkdir -p /opt/pkgconfig/ &&     for lib in ncurses form panel menu; do rm -vf /opt/lib/lib${lib}.so; echo "INPUT(-l${lib}w)" > /opt/lib/lib${lib}.so; ln -sfv ${lib}w.pc /opt/pkgconfig/${lib}.pc; done && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt