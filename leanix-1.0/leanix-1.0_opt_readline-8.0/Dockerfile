ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_ncursesw-6.2:${IMAGE_ARCH} AS opt_ncursesw

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_ncursesw /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/readline-8.0.tar.gz || \
                        wget -t 3 https://ftp.gnu.org/gnu/readline/readline-8.0.tar.gz && \
                        echo "e339f51971478d369f8a053a330a190781acb9864cf4c541060f12078948e461  readline-8.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf readline-8.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../readline-8.0/configure \
                             --prefix=/opt \
                             --disable-static && \
                             make -j$(nproc) SHLIB_LIBS="-L/opt/lib -lncurses" && \
                             make install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch
    
FROM scratch

COPY --from=build /opt /opt

