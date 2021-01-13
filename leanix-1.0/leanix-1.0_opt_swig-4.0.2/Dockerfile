ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/swig-4.0.2.tar.gz || \
                       wget -t 3 http://prdownloads.sourceforge.net/swig/swig-4.0.2.tar.gz && \
                       echo "d53be9730d8d58a16bf0cbd1f8ac0c0c3e1090573168bfa151b01eb47fa906fc  swig-4.0.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf swig-4.0.2.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../swig-4.0.2/configure \
                                  --prefix=/opt && \
                             make && \
                             make install && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

