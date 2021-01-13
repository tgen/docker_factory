ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_pcre2-10.35:${IMAGE_ARCH} AS opt_pcre
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libffi-3.3:${IMAGE_ARCH} AS opt_libffi

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_pcre /opt /opt
COPY --from=opt_libffi /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/pkgconfig/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/glib-2.64.3.tar.xz || \
                       wget -t 3 https://download.gnome.org/sources/glib/2.64/glib-2.64.3.tar.xz && \
                       echo "fe9cbc97925d14c804935f067a3ad77ef55c0bbe9befe68962318f5a767ceb22  glib-2.64.3.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf glib-2.64.3.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             meson \
                             --prefix=/opt \
                             -Dman=false \
                             -Dselinux=disabled \
                             ../glib-2.64.3 && \
                             ninja && ninja install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

