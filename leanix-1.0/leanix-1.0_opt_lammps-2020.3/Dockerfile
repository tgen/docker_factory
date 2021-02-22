ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/lammps-stable_3Mar2020.tar.gz || \
                       wget -t 3 https://github.com/lammps/lammps/archive/stable_3Mar2020.tar.gz -O lammps-stable_3Mar2020.tar.gz && \
                       echo "a1a2e3e763ef5baecea258732518d75775639db26e60af1634ab385ed89224d1  lammps-stable_3Mar2020.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf lammps-stable_3Mar2020.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             cmake -DCMAKE_INSTALL_PREFIX=/opt \
                                   -DCMAKE_BUILD_TYPE=Release \
                                   -DBUILD_LIB=ON \
                                   -DBUILD_SHARED_LIBS=ON \
                                   -DBUILD_MPI=OFF \
                                   -DPKG_MOLECULE=ON \
                                   -G "Ninja" \
                                   ../lammps-stable_3Mar2020/cmake/ && \
                             ninja && ninja install && \
    cd /tmp/scratch/lammps-stable_3Mar2020/ && cp src/STUBS/mpi.h /opt/include/lammps/ && \
    cd /tmp/scratch/lammps-stable_3Mar2020/ && cp ./src/*.h /opt/include/lammps/ && \
    sed -i 's|protected:|public:|g' /opt/include/lammps/integrate.h && \
    sed -i 's|protected:|public:|g' /opt/include/lammps/verlet.h && \
    sed -i 's|private:|public:|g' /opt/include/lammps/thermo.h && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

