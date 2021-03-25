ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

SHELL [ "bash", "-c" ]

ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib/pkgconfig/

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/ncurses-6.2.tar.gz || \
                       wget -t 3 http://ftp.gnu.org/gnu/ncurses/ncurses-6.2.tar.gz && \
                       echo "30306e0c76e0f9f1f0de987cf1c82a5c21e1ce6568b9227f7da5b71cbea86c9d  ncurses-6.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf ncurses-6.2.tar.gz && \
    cd /tmp/scratch/ncurses-6.2 && sed -i s/mawk// configure && \
                                   CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   ./configure \
                                    --prefix=/opt \
                                    --with-static   \
                                    --without-debug \
                                    --without-ada   \
                                    --enable-widec  \
                                    --enable-overwrite && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    ln -s libncursesw.a /opt/lib/libncurses.a

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/bash-5.0.tar.gz || \
                       wget -t 3 http://ftp.gnu.org/gnu/bash/bash-5.0.tar.gz && \
                       echo "b4a80f2ac66170b2913efbfb9f2594f1f76c7b1afd11f799e22035d63077fb4d  bash-5.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf bash-5.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             LDFLAGS="-L/opt/lib/" \
                             ../bash-5.0/configure \
                              --prefix=/opt \
                              --without-bash-malloc && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
                             ln -sv bash /opt/bin/sh && \
    cd / && rm -rf /tmp/scratch && \
    rm -f /bin/sh && \
    rm -f /bin/bash && \
    ln -s /opt/bin/bash /bin/sh && \
    ln -s /opt/bin/bash /bin/bash

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/coreutils-8.31.tar.xz || \
                       wget -t 3 ftp://ftp.gnu.org/gnu/coreutils/coreutils-8.31.tar.xz && \
                       echo "ff7a9c918edce6b4f4b2725e3f9b37b0c4d193531cac49a48b56c4d0d3a9e9fd  coreutils-8.31.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf coreutils-8.31.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             FORCE_UNSAFE_CONFIGURE=1 \
                             ../coreutils-8.31/configure \
                               --prefix=/opt \
                               --enable-install-program=hostname && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/which-2.21.tar.gz || \
                       wget -t 3 ftp://ftp.gnu.org/gnu/which/which-2.21.tar.gz && \
                       echo "f4a245b94124b377d8b49646bf421f9155d36aa7614b6ebf83705d3ffc76eaad  which-2.21.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf which-2.21.tar.gz && \
    cd /tmp/scratch/which-2.21 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  ./configure \
                                     --prefix=/opt && \
                                  make -j$(nproc) && \
                                  make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/findutils-4.7.0.tar.xz || \
                       wget -t 3 http://ftp.gnu.org/gnu/findutils/findutils-4.7.0.tar.xz && \
                       echo "c5fefbdf9858f7e4feb86f036e1247a54c79fc2d8e4b7064d5aaa1f47dfa789a  findutils-4.7.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf findutils-4.7.0.tar.xz && \
    cd /tmp/scratch/findutils-4.7.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/opt && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && wget -t 1 ${LOCAL_SOURCES_SERVER}/sed-4.8.tar.xz || \
                       wget -t 3 ftp://ftp.gnu.org/gnu/sed/sed-4.8.tar.xz && \
                       echo "f79b0cfea71b37a8eeec8490db6c5f7ae7719c35587f21edb0617f370eeff633  sed-4.8.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf sed-4.8.tar.xz && \
    cd /tmp/scratch/sed-4.8 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ./configure \
                                  --prefix=/opt && \
                               make && \
                               make install && \
    cd / && rm -rf /tmp/scratch

RUN rm -f /opt/lib/*.a && \
    rm -f /opt/bin/x86_64-* && \
    rm -rf /opt/lib/*.la && \
    rm -rf /opt/lib/pkgconfig && \
    strip --strip-unneeded /opt/lib/* && \
    /usr/bin/strip --strip-unneeded /opt/{,s}bin/* || true && \
    rm -rf /opt/{,share}/{info,man,doc} && \
    find /opt/{lib,libexec} -name \*.la -delete

FROM scratch

COPY --from=build /bin/bash /bin/bash
COPY --from=build /bin/sh /bin/sh
COPY --from=build /tools/lib/libc.so.6 /opt/lib/
COPY --from=build /tools/lib/libdl.so.2 /opt/lib/
COPY --from=build /tools/lib/libm.so.6 /opt/lib/
COPY --from=build /tools/lib/libpthread.so.0 /opt/lib/
COPY --from=build /tools/lib/librt.so.1 /opt/lib/
COPY --from=build /tools/lib/libcrypt.so.1 /opt/lib/
COPY --from=build /tools/lib/libutil.so.1 /opt/lib/
COPY --from=build /tools/lib/libgcc_s.so.1 /opt/lib/
COPY --from=build /tools/lib/libstdc++.so.6 /opt/lib/
COPY --from=build /tools/lib/libgfortran.so.5 /opt/lib/
COPY --from=build /tools/lib/libquadmath.so.0 /opt/lib/
COPY --from=build /tools/lib/libnss_files.so.2 /opt/lib/
COPY --from=build /tools/lib/libgomp.so.1 /opt/lib/
COPY --from=build /tools/lib/libmvec.so.1 /opt/lib/
COPY --from=build /tools/lib/libresolv.so.2 /opt/lib/
COPY --from=build /tools/lib/libnss_dns.so.2 /opt/lib/
COPY --from=build /tools/lib/libnsl.so.1 /opt/lib/
COPY --from=build /tools/lib64/ld-linux-x86-64.so.2 /tools/lib64/ld-linux-x86-64.so.2
COPY --from=build /opt/bin/ /opt/bin/
COPY --from=build /opt/lib/ /opt/lib/
COPY --from=build /tools/bin/ldd /opt/bin/
COPY --from=build /etc/group /etc/group
COPY --from=build /etc/passwd /etc/group
COPY --from=build /etc/nsswitch.conf /etc/nsswitch.conf
COPY --from=build /usr/share/zoneinfo /usr/share/zoneinfo

ENV PATH /opt/bin/
ENV LD_LIBRARY_PATH /opt/lib/

#RUN rm -f /bin/sh && \
#    rm -f /bin/bash && \
#    ln -s /opt/bin/bash /bin/sh && \
#    ln -s /opt/bin/bash /bin/bash && \
RUN mkdir -p /lib64/ && ln -s /tools/lib64/ld-linux-x86-64.so.2 /lib64/ld-linux-x86-64.so.2

RUN mkdir -p /usr && rm -rf /usr/bin && ln -s /opt/bin /usr/bin && \
    rm -rf /bin && ln -s /opt/bin /bin && \
    mkdir -p /tools && ln -s /opt/lib /tools/

RUN echo $'root:x:0:0:root:/root:/bin/bash \n\
' > /etc/passwd

RUN echo $'root:x:0: \n\
bin:x:1: \n\
sys:x:2: \n\
kmem:x:3: \n\
tty:x:4: \n\
tape:x:5: \n\
daemon:x:6: \n\
floppy:x:7: \n\
disk:x:8: \n\
lp:x:9: \n\
dialout:x:10: \n\
audio:x:11: \n\
video:x:12: \n\
utmp:x:13: \n\
usb:x:14: \n\
' > /etc/group

RUN mkdir -p /tmp && chmod a+rwx -R /tmp

