ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openssl-1.1.1g:${IMAGE_ARCH} AS opt_openssl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_openssl /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/perl-5.32.0.tar.gz || \
                        wget -t 3 https://www.cpan.org/src/5.0/perl-5.32.0.tar.gz && \
                        echo "efeb1ce1f10824190ad1cadbcccf6fdb8a5d37007d0100d2d9ae5f2b5900c0b4  perl-5.32.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xf perl-5.32.0.tar.gz && \
    rm -f /usr/lib/x86_64-linux-gnu/libcrypt.so* && \
    cd /tmp/scratch/perl-5.32.0/ && CFLAGS="-O3 -march=${IMAGE_ARCH} -Dusethreads" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH} -Dusethreads" \
                                    ./configure.gnu --prefix=/opt -Dusethreads && \
                                    make -j$(nproc) && \
                                    make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN cpan CPAN

FROM scratch

COPY --from=build /opt /opt

