ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_ncursesw-6.2:${IMAGE_ARCH} AS opt_ncursesw
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_htslib /opt /opt
COPY --from=opt_ncursesw /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}
ENV SSL_CERT_FILE /opt/ssl/certs/ca-certificates.crt

#RUN ln -s libncurses.so /opt/lib/libcurses.so
#RUN ls -lart /opt/lib/libcurses.so
#RUN false

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/samtools-1.10.tar.bz2 || \
                       wget -t 3 https://github.com/samtools/samtools/releases/download/1.10/samtools-1.10.tar.bz2 && \
                       echo "7b9ec5f05d61ec17bd9a82927e45d8ef37f813f79eb03fe06c88377f1bd03585  samtools-1.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xf samtools-1.10.tar.bz2 && \
    ln -s /opt/lib/libncurses.so /tools/lib/libcurses.so && \
    cd /tmp/scratch/samtools-1.10/ && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/ -L/opt/lib/" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH} -L/opt/lib/" \
                                      LDFLAGS="-L/opt/lib/" \
                                      ./configure \
                                        --prefix=/opt \
                                        --enable-plugins \
                                        --enable-libcurl \
                                        --with-htslib=/opt && \
                                      make -j$(nproc) && \
                                      make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt