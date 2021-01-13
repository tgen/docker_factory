ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/flex-2.6.4.tar.gz || \
                        wget -t 3 https://github.com/westes/flex/releases/download/v2.6.4/flex-2.6.4.tar.gz && \
                        echo "e87aae032bf07c26f85ac0ed3250998c37621d95f8bd748b31f15b33c45ee995  flex-2.6.4.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xvf flex-2.6.4.tar.gz && \
    cd /tmp/scratch/flex-2.6.4 && sed -i "/math.h/a #include <malloc.h>" src/flexdef.h && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../flex-2.6.4/configure \
                                --prefix=/opt && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    ln -sv flex /opt/bin/lex && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

