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

ENV EXPATLIBPATH /opt/lib
ENV EXPATINCPATH /opt/include
ENV HTSLIB_DIR /opt/

RUN mkdir -p /usr/local/BerkeleyDB/ && ln -s /opt/include /usr/local/BerkeleyDB/ && \
                                       ln -s /opt/lib /usr/local/BerkeleyDB/
RUN cpan App::cpanminus && \
    cpanm Error && \
    cpanm HTTP::Date && \
    cpanm Module::Build && \
    cpanm List::MoreUtils && \
    cpanm List::MoreUtils::XS && \
    cpanm Set::Scalar && \
    cpanm Test::Most && \
    cpanm Test::Base && \
    cpanm Test::Weaken && \
    cpanm Test::LeakTrace && \
    cpanm Test::Memory::Cycle && \
    cpanm IO::String && \
    cpanm XML::LibXML && \
    cpanm XML::LibXML::Reader && \
    cpanm XML::Writer && \
    cpanm XML::Parser && \
    cpanm XML::Parser::PerlSAX && \
    cpanm XML::Twig && \
    cpanm XML::SAX && \
    cpanm XML::SAX::Base && \
    cpanm XML::SAX::Writer && \
    cpanm XML::DOM && \
    cpanm XML::DOM::XPath --force && \
    cpanm YAML && \
    cpanm URI && \
    cpanm IO::Scalar && \
    cpanm Alien::Build && \
    cpanm Alien::Libxml2 && \
    cpanm DB_File && \
    cpanm DBI && \
    cpanm Exporter::Tiny && \
    cpanm Data::Stag && \
    cpanm Graph::Directed && \
    cpanm IPC::Run

RUN cpanm XML::Simple && \
    cpanm Array::Compare && \
    cpanm Bio::Phylo && \
    cpanm HTML::TableExtract && \
    cpanm Sort::Naturally

RUN cpanm DBD::SQLite
RUN cpanm Algorithm::Munkres && \
    cpanm Clone && \
    cpanm Convert::Binary::C
RUN cpanm PostScript::TextBlock && \
    cpanm SOAP::Lite && \
    cpanm SVG && \
    cpanm SVG::Graph && \
    cpanm Spreadsheet::ParseExcel

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/BioPerl-1.6.924.tar.gz || \
                         wget -t 3 https://cpan.metacpan.org/authors/id/C/CJ/CJFIELDS/BioPerl-1.6.924.tar.gz  ) && \
                       echo "616a7546bb3c58504de27304a0f6cb904e18b6bbcdb6a4ec8454f2bd37bb76d0  BioPerl-1.6.924.tar.gz" | sha256sum -c && \
                       tar -xvf BioPerl-1.6.924.tar.gz && \
    cd /tmp/scratch/BioPerl-1.6.924 && perl ./Build.PL && ./Build && ./Build install && \ 
    rm -rf /tmp/scratch
    
RUN mkdir -p /opt && \
    cd /opt/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/vep-98.zip || \
                  wget -t 3 https://github.com/Ensembl/ensembl-vep/archive/release/98.zip -O vep-98.zip ) && \
                echo "c11d949dbb25964fa8cef365efdeb1ec3896ba02c4cc67e1c4a2dfcd4b5e35f1  vep-98.zip" | sha256sum -c && \
    cd /opt/ && unzip vep-98.zip && \
    cd /opt/ && rm vep-98.zip && \
    cd /opt/ && mv ensembl-vep-release-98 ensembl-vep && \
    cd /opt/ensembl-vep/ && ./INSTALL.pl -a a && \
    mkdir -p /opt/bin && ln -s /opt/ensembl-vep/vep /opt/bin/ && \
    rm -rf /opt/ensembl-vep/t/test* /opt/ensembl-vep/examples

RUN ln -s DB /opt/lib/perl5/site_perl/5.32.0/Bio/EnsEMBL

FROM scratch

COPY --from=build /opt /opt

