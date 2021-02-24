ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_gsl-2.6:${IMAGE_ARCH} AS opt_gsl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_gsl /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/artsrcmountrainier2016.06.05linux.tgz || \
                       wget -t 3 https://www.niehs.nih.gov/research/resources/assets/docs/artsrcmountrainier2016.06.05linux.tgz && \
                       echo "69aede60884eb848de043aae5294274b7ca6348b7384a8380f0ac5a4dfeff488  artsrcmountrainier2016.06.05linux.tgz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf artsrcmountrainier2016.06.05linux.tgz && \
    cd /tmp/scratch/art_src_MountRainier_Linux/ && \
                                  make distclean && \
                                  CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                                  LDFLAGS="-L/opt/lib/" \
                                  ./configure \
                                    --prefix=/opt && \
                                  make -j$(nproc) && \
                                  make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt
