ARG SOURCE_DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_openjdk-8u:${IMAGE_ARCH} AS opt_openjdk
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_bcftools-1.10.2:${IMAGE_ARCH} AS opt_bcftools

FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 3 https://snpeff.blob.core.windows.net/versions/snpEff_latest_core.zip && \
                       echo "85d907b5dd9e9008a0cf245956e3c9077a31e45f21a1b580d9b98a53fd8dcb9d  snpEff_latest_core.zip" | sha256sum -c && \
    cd /tmp/scratch && unzip snpEff_latest_core.zip && mv snpEff /opt/ && \
    cd / && rm -rf /tmp/scratch
        
FROM ${SOURCE_DOCKER_REGISTRY}/leanix-1.0_opt_base:${IMAGE_ARCH}

COPY --from=opt_openjdk /opt/ /opt/
COPY --from=opt_bcftools /opt/ /opt/
COPY --from=build /opt /opt

RUN mkdir -p /opt/bin/ && echo -e '#!/bin/bash\njava -Xmx8G -jar /opt/snpEff/snpEff.jar $@' > /opt/bin/snpEff && chmod +x /opt/bin/snpEff

ENV PATH /opt/java/bin/:/opt/miniconda/bin/:${PATH}
ENV JAVA_HOME /opt/java

RUN cd /opt/snpEff/ && java -jar snpEff.jar download GRCh38.98

LABEL org.opencontainers.image.source=https://github.com/tgen/jetstream_containers
