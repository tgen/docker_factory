ARG SOURCE_DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_vep-98:${IMAGE_ARCH} as opt_vep

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

COPY --from=opt_vep /opt/ /opt/

RUN rm -f /opt/lib/*.a && \
    rm -f /opt/bin/x86_64-* && \
    rm -rf /opt/lib/*.la && \
    rm -rf /opt/lib/pkgconfig && \
    strip --strip-unneeded /opt/lib/* && \
    /usr/bin/strip --strip-unneeded /opt/{,s}bin/* || true && \
    rm -rf /opt/{,share}/{info,man,doc} && \
    find /opt/{lib,libexec} -name \*.la -delete

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_base:${IMAGE_ARCH}

COPY --from=build /opt/ /opt/

LABEL org.opencontainers.image.source=https://github.com/tgen/docker_factory
