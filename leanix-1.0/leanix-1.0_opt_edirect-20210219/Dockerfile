ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_perl-5.32.0:${IMAGE_ARCH} as opt_perl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_expat-2.2.9:${IMAGE_ARCH} AS opt_expat

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_perl /opt /opt
COPY --from=opt_expat /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN cpan App::cpanminus
ENV EXPATLIBPATH /opt/lib
ENV EXPATINCPATH /opt/include
RUN cpanm XML::Simple

RUN mkdir -p /opt/ && \
    cd /opt/ && \
    perl -MNet::FTP -e \
      '$ftp = new Net::FTP("ftp.ncbi.nlm.nih.gov", Passive => 1); \
       $ftp->login; $ftp->binary; \
       $ftp->get("/entrez/entrezdirect/edirect.tar.gz");' && \
    gunzip -c edirect.tar.gz | tar xf - && \
    rm edirect.tar.gz && \
    ./edirect/setup.sh

FROM scratch

COPY --from=build /opt /opt

