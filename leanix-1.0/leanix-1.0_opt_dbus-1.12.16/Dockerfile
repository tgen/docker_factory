ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_expat-2.2.9:${IMAGE_ARCH} AS opt_expat

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

COPY --from=opt_expat /opt /opt

ENV PATH /opt/bin/:${PATH}
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/dbus-1.12.16.tar.gz || \
                       wget -t 3 https://dbus.freedesktop.org/releases/dbus/dbus-1.12.16.tar.gz && \
                       echo "54a22d2fa42f2eb2a871f32811c6005b531b9613b1b93a0d269b05e7549fec80  dbus-1.12.16.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf dbus-1.12.16.tar.gz && \
    cd /tmp/scratch/dbus-1.12.16 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    EXPAT_CFLAGS="-O3 -march=${IMAGE_ARCH} -I/opt/include/" \
                                    EXPAT_LIBS="-L/opt/lib/ -lexpat" \
                                      ./configure --prefix=/opt                        \
                                                  --sysconfdir=/opt                    \
                                                  --localstatedir=/opt                 \
                                                  --enable-user-session                \
                                                  --disable-doxygen-docs               \
                                                  --disable-xml-docs                   \
                                                  --disable-static                     \
                                                  --with-systemduserunitdir=no         \
                                                  --with-systemdsystemunitdir=no       \
                                                  --docdir=/usr/share/doc/dbus-1.12.18 \
                                                  --with-console-auth-dir=/opt/console \
                                                  --with-system-pid-file=/opt/dbus/pid \
                                                  --with-system-socket=/opt/dbus/system_bus_socket && \
                                      make -j$(nproc) && make -j$(nproc) install && \
    rm -rf /opt/lib/*.a && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

