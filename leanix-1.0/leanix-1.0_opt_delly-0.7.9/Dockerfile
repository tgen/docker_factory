ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_htslib-1.10.2:${IMAGE_ARCH} AS opt_htslib
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_curl-7.70.0:${IMAGE_ARCH} AS opt_curl

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_boost /opt /opt
COPY --from=opt_htslib /opt /opt
COPY --from=opt_curl /opt /opt

#RUN find / -name libhts.so.2 && false
ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/ && \
    cd /tmp/scratch/ && git clone --recursive --single-branch --branch v0.7.9  https://github.com/dellytools/delly.git && \
    cd /tmp/scratch/delly/ && sed -i 's|std::max(|std::max<int>(|g' src/*.h && \
    cd /tmp/scratch/delly/ && sed -i 's|std::min(|std::min<int>(|g' src/*.h && \
    cd /tmp/scratch/delly/ && CXXFLAGS="-Wno-pedantic -Wno-unused-local-typedefs -Wno-deprecated-copy -Wno-unused-result -I/opt/include/" LDFLAGS="-L/opt/lib/" make -j$(nproc) src/delly && \
    mkdir -p /opt/bin && \
    cd /tmp/scratch/delly/ && mv src/delly /opt/bin/ && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt/bin /opt/bin
COPY --from=build /opt/lib /opt/lib

