ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openjdk-8u:${IMAGE_ARCH} AS opt_openjdk
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_r-4.0.2.headless.genomic:${IMAGE_ARCH} AS opt_r_genomic
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_miniconda3-4.8.2:${IMAGE_ARCH} AS opt_miniconda

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && mkdir -p /opt/gatk/ && \
    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/gatk-4.1.4.1.zip || \
                        wget -t 3 https://github.com/broadinstitute/gatk/releases/download/4.1.4.1/gatk-4.1.4.1.zip && \
                        echo "21ae694cfc8b7447381ad5ce62ed4af22e53a228b12495bdcca7df0c73b09cea  gatk-4.1.4.1.zip" | sha256sum -c && \
    cd /opt/gatk/ && unzip /tmp/scratch/gatk-4.1.4.1.zip && \
    cd / && rm -rf /tmp/scratch

COPY --from=opt_miniconda /opt /opt

ENV PATH /opt/miniconda/bin/:/opt/bin/:/usr/bin/:/bin/:/usr/sbin/:/sbin/
ENV LD_LIBRARY_PATH /opt/lib/

COPY gatkcondaenv.yml /opt/gatk/gatk-4.1.4.1/
RUN cd /opt/gatk/gatk-4.1.4.1/ && conda env create -f gatkcondaenv.yml
RUN conda clean -y --all && \
    echo "source activate gatk" >> /opt/gatk/gatk-4.1.4.1/gatkenv.rc && \
    echo "source /opt/gatk/gatk-4.1.4.1/gatk-completion.sh" >> /opt/gatk/gatk-4.1.4.1/gatkenv.rc

RUN python -m pip install gatktool

COPY run.sh /opt/gatk/run.sh
RUN /tools/bin/chmod +x /opt/gatk/run.sh && \
    /tools/bin/mkdir -p /opt/bin/ && /tools/bin/ln -s /opt/gatk/gatk-4.1.4.1/gatk /opt/bin/gatk

FROM scratch

COPY --from=opt_openjdk /opt /opt
COPY --from=opt_r_genomic /opt /opt
COPY --from=build /opt /opt
