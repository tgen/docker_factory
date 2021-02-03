ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_db-6.2.32:${IMAGE_ARCH} AS opt_db
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_perl-5.32.0:${IMAGE_ARCH} AS opt_perl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_expat-2.2.9:${IMAGE_ARCH} AS opt_expat
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_libxml2-2.9.10:${IMAGE_ARCH} AS opt_libxml2

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_db /opt /opt
COPY --from=opt_curl /opt /opt
COPY --from=opt_perl /opt /opt
COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_htslib /opt /opt
COPY --from=opt_expat /opt /opt
COPY --from=opt_libxml2 /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib/:${LD_LIBRARY_PATH}

RUN cpan App::cpanminus
ENV EXPATLIBPATH /opt/lib
ENV EXPATINCPATH /opt/include
RUN cpanm XML::Parser --force

RUN cpanm URI
RUN cpanm Alien::Build
RUN cpanm Alien::Libxml2
RUN cpanm DBI
RUN cpanm Test::LeakTrace
RUN cpanm Exporter::Tiny
RUN cpanm List::MoreUtils::XS
RUN cpanm XML::Twig
RUN cpanm Data::Stag
RUN cpanm Graph::Directed
RUN cpanm Test::Memory::Cycle
RUN cpanm XML::LibXML
RUN cpanm XML::LibXML::Reader

RUN cpanm Error
RUN cpanm Set::Scalar
RUN cpanm XML::SAX
RUN cpanm Test::Most
RUN cpanm Test::Base
RUN cpanm YAML
RUN cpanm Module::Build

RUN cpanm XML::Writer
RUN cpanm XML::Parser::PerlSAX
RUN cpanm IO::Scalar
RUN cpanm XML::SAX::Base
RUN cpanm List::MoreUtils
RUN mkdir -p /usr/local/BerkeleyDB/ && ln -s /opt/include /usr/local/BerkeleyDB/ && \
                                       ln -s /opt/lib /usr/local/BerkeleyDB/ && \
    cpanm DB_File
RUN cpanm XML::SAX::Writer
RUN cpanm IO::String
RUN cpanm Test::Weaken
RUN cpanm IPC::Run
RUN cpanm XML::DOM
RUN cpanm XML::DOM::XPath --force
RUN cpanm BioPerl --force
RUN HTSLIB_DIR=/opt/ cpanm Bio::DB::HTS::Tabix

RUN mkdir -p /opt && \
    cd /opt/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/vep-98.zip || \
                  wget -t 3 https://github.com/Ensembl/ensembl-vep/archive/release/98.zip -O vep-98.zip ) && \
                echo "c11d949dbb25964fa8cef365efdeb1ec3896ba02c4cc67e1c4a2dfcd4b5e35f1  vep-98.zip" | sha256sum -c && true && \
    cd /opt/ && unzip vep-98.zip && \
    cd /opt/ && rm vep-98.zip && \
    cd /opt/ && mv ensembl-vep-release-98 ensembl-vep && \
    cd /opt/ensembl-vep/ && ./INSTALL.pl -a a && \
    cd /opt/ensembl-vep/ && mkdir -p /opt/bin && cp ./vep /opt/bin/ && \
    rm -rf /opt/ensembl-vep/t/test* /opt/ensembl-vep/examples

RUN ln -s DB /opt/lib/perl5/site_perl/5.32.0/Bio/EnsEMBL

FROM scratch

COPY --from=build /opt /opt

