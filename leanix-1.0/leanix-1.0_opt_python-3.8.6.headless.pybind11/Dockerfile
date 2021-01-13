ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_python-3.8.6.headless:${IMAGE_ARCH} AS opt_python
FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_boost-1.74.0:${IMAGE_ARCH} AS opt_boost

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/:${PKG_CONFIG_PATH}
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

COPY --from=opt_python /opt /opt
COPY --from=opt_boost /opt /opt

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/pybind11-2.5.0.tar.gz || \
                       wget -t 3 https://github.com/pybind/pybind11/archive/v2.5.0.tar.gz -O pybind11-2.5.0.tar.gz && \
                       echo "97504db65640570f32d3fdf701c25a340c8643037c3b69aec469c10c93dc8504  pybind11-2.5.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf pybind11-2.5.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake -DCMAKE_INSTALL_PREFIX=/opt \
                                   -DCMAKE_BUILD_TYPE=Release \
                                   -DBUILD_SHARED_LIBS=ON \
                                   -DPYBIND11_TEST=OFF \
                                   -DPYBIND11_INSTALL=ON \
                                   -G "Ninja" \
                                   ../pybind11-2.5.0/ && \
                             ninja && ninja install && \
    cd / && rm -rf /tmp/scratch/build

FROM scratch

COPY --from=build /opt /opt
