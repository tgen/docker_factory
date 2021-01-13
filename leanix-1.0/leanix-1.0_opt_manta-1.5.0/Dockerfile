ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_miniconda2-4.7.12.1:${IMAGE_ARCH} AS opt_miniconda

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

COPY --from=opt_miniconda /opt /opt

RUN /opt/miniconda/bin/conda install -c bioconda/label/cf201901 manta

FROM scratch

COPY --from=build /opt /opt

