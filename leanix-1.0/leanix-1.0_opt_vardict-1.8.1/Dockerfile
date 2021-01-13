ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_openjdk-8u:${IMAGE_ARCH} AS opt_java
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_perl-5.32.0:${IMAGE_ARCH} AS opt_perl
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_r-4.0.2.headless:${IMAGE_ARCH} AS opt_r

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

#RUN mkdir -p /tmp/scratch/ && \
#    cd /tmp/scratch/ && wget -t 1 ${LOCAL_SOURCES_SERVER}/VarDict-1.8.1.tar.gz || \
#                        wget -t 3 https://github.com/AstraZeneca-NGS/VarDictJava/archive/v1.8.1.tar.gz -O VarDict-1.8.1.tar.gz && \
#                        echo "2a000b1637f75a320cd06033e494b76b59674a5170de440d7d6616b0969d6992  VarDict-1.8.1.tar.gz" | sha256sum -c && \
#    cd /tmp/scratch/ && tar -xvf VarDict-1.8.1.tar.gz && rm VarDict-1.8.1.tar.gz

COPY --from=opt_java /opt /opt

RUN mkdir -p /tmp/scratch/ && \
    cd /tmp/scratch/ && git clone --recursive https://github.com/AstraZeneca-NGS/VarDictJava.git

ENV PATH /opt/java/bin/:${PATH}

RUN cd /tmp/scratch/VarDictJava/ && \
    ./gradlew clean installDist && \
    ./gradlew distTar && \
    mv /tmp/scratch/VarDictJava/build/install/VarDict/bin/* /opt/bin/ && \
    mv /tmp/scratch/VarDictJava/build/install/VarDict/lib/* /opt/lib/ && \
    rm -rf /tmp/scratch

FROM scratch

COPY --from=opt_perl /opt /opt
COPY --from=opt_r /opt /opt
COPY --from=build /opt /opt
