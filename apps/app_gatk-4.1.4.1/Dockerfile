ARG SOURCE_DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_gatk-4.1.4.1:${IMAGE_ARCH} AS opt_gatk
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_bcftools-1.10.2:${IMAGE_ARCH} AS opt_bcftools
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_gawk-5.0.1:${IMAGE_ARCH} AS opt_gawk
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_grep-3.4:${IMAGE_ARCH} AS opt_grep

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

COPY --from=opt_gatk /opt/ /opt/
COPY --from=opt_bcftools /opt/ /opt/
COPY --from=opt_gawk /opt/ /opt/
COPY --from=opt_grep /opt/ /opt/

RUN rm -f /opt/lib/*.a && \
    rm -f /opt/bin/x86_64-* && \
    rm -rf /opt/lib/*.la && \
    rm -rf /opt/lib/pkgconfig && \
    strip --strip-unneeded /opt/lib/* && \
    /usr/bin/strip --strip-unneeded /opt/{,s}bin/* || true && \
    /usr/bin/strip --strip-unneeded /opt/java/bin/* || true && \
    /usr/bin/strip --strip-unneeded /opt/miniconda/bin/* || true && \
    rm -rf /opt/{,share}/{info,man,doc} && \
    find /opt/ -name "*.la" -delete && \
    find /opt/ -name "*.a" -delete

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_base:${IMAGE_ARCH}

COPY --from=build /opt/ /opt/

ENV PATH /opt/java/bin/:/opt/miniconda/bin/:${PATH}
#ENV LD_LIBRARY_PATH /opt/lib/
ENV JAVA_HOME /opt/java
ENV R_HOME /opt

ENTRYPOINT ["/opt/gatk/run.sh"]

LABEL org.opencontainers.image.source=https://github.com/tgen/jetstream_containers


