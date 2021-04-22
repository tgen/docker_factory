ARG SOURCE_DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} as build

RUN mkdir -p /opt/bin/ && \
    wget http://www.sbg.bio.ic.ac.uk/maxcluster/maxcluster64bit -O /opt/bin/maxcluster && \
    chmod a+x /opt/bin/maxcluster

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_base:${IMAGE_ARCH}

COPY --from=build /opt/ /opt/

LABEL org.opencontainers.image.source=https://github.com/tgen/docker_factory
