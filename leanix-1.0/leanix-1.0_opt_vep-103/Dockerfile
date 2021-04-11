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

RUN cpanm XML::Parser --force && \
    cpanm URI && \
    cpanm Alien::Build && \
    cpanm Alien::Libxml2 && \
    cpanm DBI && \
    cpanm Test::LeakTrace && \
    cpanm Exporter::Tiny && \
    cpanm List::MoreUtils::XS && \
    cpanm XML::Twig && \
    cpanm Data::Stag && \
    cpanm Graph::Directed && \
    cpanm Test::Memory::Cycle && \
    cpanm XML::LibXML && \
    cpanm XML::LibXML::Reader && \
    cpanm Error && \
    cpanm Set::Scalar && \
    cpanm XML::SAX && \
    cpanm Test::Most && \
    cpanm Test::Base && \
    cpanm YAML && \
    cpanm Module::Build && \
    cpanm XML::Writer && \
    cpanm XML::Parser::PerlSAX && \
    cpanm IO::Scalar && \
    cpanm XML::SAX::Base && \
    cpanm List::MoreUtils && \
    mkdir -p /usr/local/BerkeleyDB/ && ln -s /opt/include /usr/local/BerkeleyDB/ && \
                                       ln -s /opt/lib /usr/local/BerkeleyDB/ && \
    cpanm DB_File && \
    cpanm XML::SAX::Writer && \
    cpanm IO::String && \
    cpanm Test::Weaken && \
    cpanm IPC::Run && \
    cpanm XML::DOM && \
    cpanm XML::DOM::XPath --force

RUN cpanm BioPerl --force

RUN HTSLIB_DIR=/opt/ cpanm Bio::DB::HTS::Tabix

RUN mkdir -p /opt && \
    cd /opt/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/vep-103.zip || \
                  wget -t 3 https://github.com/Ensembl/ensembl-vep/archive/release/103.zip -O vep-103.zip ) && \
                echo "05649c6814293108958380770d9a85ec06b94dccaf6c649e4e449c680815580f  vep-103.zip" | sha256sum -c && true && \
    cd /opt/ && unzip vep-103.zip && \
    cd /opt/ && rm vep-103.zip && \
    cd /opt/ && mv ensembl-vep-release-103 ensembl-vep && \
    mkdir -p /usr && ln -s /opt/include /usr/ && \
    cd /opt/ensembl-vep/ && ./INSTALL.pl -a a && \
    cd /opt/ensembl-vep/ && mkdir -p /opt/bin && mv ./vep /opt/bin/ && \
    rm -rf /opt/ensembl-vep/t/test* /opt/ensembl-vep/examples

RUN ln -s DB /opt/lib/perl5/site_perl/5.32.0/Bio/EnsEMBL
ENV PERL5LIB /opt/ensembl-vep/modules/:/opt/ensembl-vep/
RUN vep --offline && false

FROM scratch

COPY --from=build /opt /opt

