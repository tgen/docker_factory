ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_bzip2-1.0.8:${IMAGE_ARCH} AS opt_bzip2
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_xz-5.2.5:${IMAGE_ARCH} AS opt_xz
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_zlib-1.2.11:${IMAGE_ARCH} AS opt_zlib

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_bzip2 /opt /opt
COPY --from=opt_xz /opt /opt
COPY --from=opt_zlib /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/freebayes-2c1e395b9bf2565ae69fe61061eb28387f56637e.tar.gz && \
                          echo "e5457238528942f86a7a82404592af19684fbaa01924780fd39606be9b803999  freebayes-2c1e395b9bf2565ae69fe61061eb28387f56637e.tar.gz" | sha256sum -c && \
                          tar -xvf freebayes-e5457238528942f86a7a82404592af19684fbaa01924780fd39606be9b803999.tar.gz ) || \
                        ( git clone -b v1.3.2 --recurse-submodules https://github.com/ekg/freebayes.git && \
                          cd freebayes && git checkout --recurse-submodules v1.3.2 ) && \
    cd /tmp/scratch && echo "#define VERSION_GIT UNKNOWN" > freebayes/src/version_git.h && \
    mkdir -p /usr/include && cp -r /opt/include/* /usr/include/ && \
    mkdir -p /usr/lib && cp /opt/lib/*.so /usr/lib/ && \
    cd /tmp/scratch/freebayes/ && sed -i 's|const uint8_t rle_auxtab|extern const uint8_t rle_auxtab|g' SeqLib/bwa/rle.h && \
    cd /tmp/scratch/freebayes/ && sed -i 's|const uint8_t rle_auxtab|extern const uint8_t rle_auxtab|g' SeqLib/fermi-lite/rle.h && \
    cd /tmp/scratch/freebayes && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 make && \
                                 mkdir -p /opt/bin && cp bin/freebayes bin/bamleftalign /opt/bin/ && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

