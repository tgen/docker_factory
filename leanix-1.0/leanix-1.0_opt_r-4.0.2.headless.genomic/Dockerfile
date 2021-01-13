ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_r-4.0.2.headless:${IMAGE_ARCH} AS opt_r
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libxml2-2.9.10:${IMAGE_ARCH} AS opt_libxml2

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH

COPY --from=opt_r /opt /opt
COPY --from=opt_libxml2 /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp && \
    mkdir -p ~/.R/ && \
    echo "CFLAGS=-O3 -march=${IMAGE_ARCH}" >> ~/.R/Makevars && \
    echo "CXXFLAGS=-O3 -march=${IMAGE_ARCH}" >> ~/.R/Makevars && \
    echo "options(Ncpus = 8)" > ~/.Rprofile && \
    echo "install.packages(\"RCurl\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "install.packages(\"XML\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "install.packages(\"optparse\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "install.packages(\"plyr\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "install.packages(\"BiocManager\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "install.packages(\"cgdsr\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "install.packages(\"tidyverse\", repos=\"http://cran.rstudio.com\")" | R --no-save && \
    echo "BiocManager::install(\"HMMcopy\")" | R --no-save && \
    echo "BiocManager::install(\"GenomeInfoDb\")" | R --no-save && \
    echo "BiocManager::install(\"GenomicRanges\")" | R --no-save && \
    cd / && rm -rf /tmp/*

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && git clone https://github.com/broadinstitute/ichorCNA.git && \
    cd /tmp/scratch/ichorCNA/ && git checkout 5bfc03ed854f0e93fe5b624c97c1290fa0053837 && \
    cd /tmp/scratch/ichorCNA/ && R CMD INSTALL --configure-args="CFLAGS=\"-O3 -march=${IMAGE_ARCH}\" CXXFLAGS=\"-O3 -march=${IMAGE_ARCH}\"" . && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

