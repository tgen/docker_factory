ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/sundials-4.1.0.tar.gz || \
                       wget -t 3 https://computing.llnl.gov/projects/sundials/download/sundials-4.1.0.tar.gz && \
                       echo "280de1c27b2360170a6f46cb3799b2aee9dff3bddbafc8b08c291a47ab258aa5  sundials-4.1.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf sundials-4.1.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake -DCMAKE_INSTALL_PREFIX=/opt \
                                   -DCMAKE_BUILD_TYPE=Release \
                                   -G "Ninja" \
                                   ../sundials-4.1.0/ && \
                             ninja && ninja install

RUN cd /tmp/scratch/sundials-4.1.0 && cp ./src/cvode/cvode_impl.h /opt/include/cvode/ && \
    cd /tmp/scratch/sundials-4.1.0 && cp ./src/ida/ida_impl.h /opt/include/ida/

FROM scratch

COPY --from=build /opt /opt

