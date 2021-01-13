ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=haswell
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_opt_x11-20200524:${IMAGE_ARCH} AS opt_x11

FROM ${DOCKER_REGISTRY}/leanix-1.0_tools:${IMAGE_ARCH} AS build

ARG IMAGE_ARCH

COPY --from=opt_x11 /opt /opt

#RUN java -version && false

#ENV PATH /opt/bin/:${PATH}
#ENV LD_LIBRARY_PATH /opt/lib64/:/opt/lib/:${LD_LIBRARY_PATH}
#ENV C_INCLUDE_PATH /opt/include/
#ENV CPLUS_INCLUDE_PATH /opt/include/

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && git clone https://github.com/AdoptOpenJDK/openjdk-jdk8u.git && \
    cd /tmp/scratch/openjdk-jdk8u/ && git checkout da50732b9786710698109b8e3d50c0fe547952da
#RUN find / -name libX11.so && false
ENV LD_LIBRARY_PATH /opt/lib/
ENV C_INCLUDE_PATH /opt/include/
ENV CPLUS_INCLUDE_PATH /opt/include/
ENV PKG_CONFIG_PATH /opt/pkgconfig/:/opt/lib64/:/opt/lib64/pkgconfig/:/opt/lib/:/opt/lib/pkgconfig/
RUN mkdir -p /usr/local/lib && ln -s /opt/lib/libX11.so /usr/local/lib/
RUN cd /tmp/scratch/openjdk-jdk8u/ && chmod +x ./configure && ./configure \
                                                                --prefix=/opt \
                                                                --with-extra-cflags="-O3 -march=${IMAGE_ARCH} -Wno-error -fcommon -fno-lifetime-dse -fno-delete-null-pointer-checks -I -std=c++03" \
                                                                --with-extra-cxxflags="-O3 -march=${IMAGE_ARCH} -Wno-error -fpermissive -I -std=c++03" \
                                                                --with-extra-ldflags="-L/opt/lib/" && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|(-1) << FIRST_TYPE|(-(uint)(1)) << FIRST_TYPE|g' hotspot/src/share/vm/code/dependencies.hpp && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|GSS_FUNCTION_TABLE_PTR ftab;|extern GSS_FUNCTION_TABLE_PTR ftab;|g' jdk/src/solaris/native/sun/security/jgss/wrapper/NativeFunc.h && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|#include "NativeFunc.h"|#include "NativeFunc.h"\nGSS_FUNCTION_TABLE_PTR ftab;|g' jdk/src/solaris/native/sun/security/jgss/wrapper/NativeFunc.c && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|#include <sys/sysctl.h>||g' jdk/src/solaris/native/java/net/PlainDatagramSocketImpl.c && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|#include <sys/sysctl.h>||g' jdk/src/solaris/native/java/net/PlainSocketImpl.c && \
    cd /tmp/scratch/openjdk-jdk8u/ && make images && \
    cd /tmp/scratch/openjdk-jdk8u/ && mkdir -p /opt/ && cp -r build/*/images/j2sdk-image /opt/java && \
    cd / && rm -rf /tmp/scratch

FROM scratch

COPY --from=build /opt /opt

