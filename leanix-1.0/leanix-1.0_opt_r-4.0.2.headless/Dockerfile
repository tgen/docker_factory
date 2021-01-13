ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_pcre2-10.35:${IMAGE_ARCH} AS opt_pcre2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_readline-8.0:${IMAGE_ARCH} AS opt_readline
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_curl /opt /opt
COPY --from=opt_pcre2 /opt /opt
COPY --from=opt_readline /opt /opt
COPY --from=opt_xz /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/R-4.0.2.tar.gz || \
                        wget -t 3 https://cran.rstudio.com/src/base/R-4/R-4.0.2.tar.gz && \
                        echo "d3bceab364da0876625e4097808b42512395fdf41292f4915ab1fd257c1bbe75  R-4.0.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf R-4.0.2.tar.gz && \
    mkdir -p /usr/local/include && ln -s /opt/include/zlib.h /usr/local/include/ && \
    cd /tmp/scratch/R-4.0.2/ && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                                LDFLAGS="-O3 -march=${IMAGE_ARCH} -L/opt/lib/" \
                                ./configure \
                                  --prefix=/opt \
                                  --with-readline=yes \
                                  --with-x=no \
                                  --with-recommended-packages=yes && \
                                make -j$(nproc) && make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

RUN sed -i 's|tools|opt|g' /opt/lib/R/bin/R

FROM scratch

COPY --from=build /opt /opt

