ARG DOCKER_REGISTRY=localhost:5000
ARG IMAGE_ARCH=sandybridge
ARG LOCAL_SOURCES_SERVER=http://localhost:8000

FROM ${DOCKER_REGISTRY}/leanix-1.0_bootstrap as tools

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

ENV LFS_TGT x86_64-lfs-linux-gnu
ENV PATH /tools/bin:/bin:/usr/bin

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/binutils-2.34.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/binutils/binutils-2.34.tar.xz ) && \
                       echo "f00b0e8803dc9bab1e2165bd568528135be734df3fabf8d0161828cd56028952  binutils-2.34.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf binutils-2.34.tar.xz && \
    cd /tmp/scratch/build && ../binutils-2.34/configure \
                                --prefix=/tools            \
                                --with-sysroot=/           \
                                --with-lib-path=/tools/lib \
                                --target=$LFS_TGT          \
                                --disable-nls              \
                                --disable-werror && \
                                make -j$(nproc) && \
                                mkdir -p /tools/lib && ln -s lib /tools/lib64 && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gcc-10.2.0.tar.xz || \
                           wget -t 3 https://ftp.gnu.org/gnu/gcc/gcc-10.2.0/gcc-10.2.0.tar.xz ) && \
                       echo "b8dd4368bb9c7f0b98188317ee0254dd8cc99d1e3a18d0ff146c855fe16c1d8c  gcc-10.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gcc-10.2.0.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gmp-6.2.0.tar.xz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/gmp/gmp-6.2.0.tar.xz ) && \
                                  echo "258e6cd51b3fbdfc185c716d55f82c08aff57df0c6fbd143cf6ed561267a1526  gmp-6.2.0.tar.xz" | sha256sum -c && \
                                  tar -xf gmp-6.2.0.tar.xz && mv gmp-6.2.0 gmp && rm gmp-6.2.0.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/mpfr-4.0.2.tar.xz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/mpfr/mpfr-4.0.2.tar.xz ) && \
                                  echo "1d3be708604eae0e42d578ba93b390c2a145f17743a744d8f3f8c2ad5855a38a  mpfr-4.0.2.tar.xz" | sha256sum -c && \
                                  tar -xf mpfr-4.0.2.tar.xz && mv mpfr-4.0.2 mpfr && rm mpfr-4.0.2.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/mpc-1.1.0.tar.gz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz ) && \
                                  echo "6985c538143c1208dcb1ac42cedad6ff52e267b47e5f970183a3e75125b43c2e  mpc-1.1.0.tar.gz" | sha256sum -c && \
                                  tar -xf mpc-1.1.0.tar.gz && mv mpc-1.1.0 mpc && rm mpc-1.1.0.tar.gz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/isl-0.22.1.tar.xz || \
                                      wget -t 3 http://isl.gforge.inria.fr/isl-0.22.1.tar.xz ) && \
                                  echo "28658ce0f0bdb95b51fd2eb15df24211c53284f6ca2ac5e897acc3169e55b60f  isl-0.22.1.tar.xz" | sha256sum -c && \
                                  tar -xf isl-0.22.1.tar.xz && mv isl-0.22.1 isl && rm isl-0.22.1.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && for file in gcc/config/{linux,i386/linux{,64}}.h; \
    do \
      echo ${file}; \
      cp -uv $file{,.orig}; \
      sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' -e 's@/usr@/tools@g' $file.orig > $file; \
      echo '#undef STANDARD_STARTFILE_PREFIX_1' >> $file; \
      echo '#undef STANDARD_STARTFILE_PREFIX_2' >> $file; \
      echo '#define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"' >> $file; \
      echo '#define STANDARD_STARTFILE_PREFIX_2 ""' >> $file; \
      touch $file.orig; \
    done && \
    cd /tmp/scratch/gcc-10.2.0 && sed -e '/m64=/s/lib64/lib/' -i.orig gcc/config/i386/t-linux64 && \
    cd /tmp/scratch/build && ../gcc-10.2.0/configure \
                                    --prefix=/tools                                \
                                    --target=$LFS_TGT                              \
                                    --with-sysroot=/                               \
                                    --with-glibc-version=2.32                      \
                                    --with-newlib                                  \
                                    --without-headers                              \
                                    --with-local-prefix=/tools                     \
                                    --with-native-system-header-dir=/tools/include \
                                    --disable-nls                                  \
                                    --disable-shared                               \
                                    --disable-multilib                             \
                                    --disable-decimal-float                        \
                                    --disable-threads                              \
                                    --disable-libatomic                            \
                                    --disable-libgomp                              \
                                    --disable-libquadmath                          \
                                    --disable-libssp                               \
                                    --disable-libvtv                               \
                                    --disable-libstdcxx                            \
                                    --enable-languages=c,c++ && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/linux-5.5.3.tar.xz || \
                         wget -t 3 https://www.kernel.org/pub/linux/kernel/v5.x/linux-5.5.3.tar.xz ) && \
                       echo "2bef3edcf44c746383045f4a809b2013e18c52319c827875ed8e89138951cab2  linux-5.5.3.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf linux-5.5.3.tar.xz && \
    cd /tmp/scratch/linux-5.5.3 && make mrproper && \
                                   make headers && \
                                   mkdir -p /tools/include && cp -rv usr/include/* /tools/include

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/glibc-2.32.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/libc/glibc-2.32.tar.xz ) && \
                       echo "1627ea54f5a1a8467032563393e0901077626dc66f37f10ee6363bb722222836  glibc-2.32.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf glibc-2.32.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -Wno-error=maybe-uninitialized" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ../glibc-2.32/configure \
                               --prefix=/tools                    \
                               --host=$LFS_TGT                    \
                               --build=$(../scripts/config.guess) \
                               --enable-kernel=3.2                \
                               --with-headers=/tools/include && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gcc-10.2.0.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/gcc/gcc-10.2.0/gcc-10.2.0.tar.xz ) && \
                       echo "b8dd4368bb9c7f0b98188317ee0254dd8cc99d1e3a18d0ff146c855fe16c1d8c  gcc-10.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gcc-10.2.0.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../gcc-10.2.0/libstdc++-v3/configure \
                             --host=$LFS_TGT                 \
                             --prefix=/tools                 \
                             --disable-multilib              \
                             --disable-nls                   \
                             --disable-libstdcxx-threads     \
                             --disable-libstdcxx-pch         \
                             --with-gxx-include-dir=/tools/$LFS_TGT/include/c++/10.2.0 && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/binutils-2.34.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/binutils/binutils-2.34.tar.xz ) && \
                       echo "f00b0e8803dc9bab1e2165bd568528135be734df3fabf8d0161828cd56028952  binutils-2.34.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf binutils-2.34.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CC=$LFS_TGT-gcc                \
                             AR=$LFS_TGT-ar                 \
                             RANLIB=$LFS_TGT-ranlib         \
                             ../binutils-2.34/configure     \
                                --prefix=/tools            \
                                --disable-nls              \
                                --disable-werror           \
                                --with-lib-path=/tools/lib \
                                --with-sysroot && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gcc-10.2.0.tar.xz || \
                           wget -t 3 https://ftp.gnu.org/gnu/gcc/gcc-10.2.0/gcc-10.2.0.tar.xz ) && \
                       echo "b8dd4368bb9c7f0b98188317ee0254dd8cc99d1e3a18d0ff146c855fe16c1d8c  gcc-10.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gcc-10.2.0.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gmp-6.2.0.tar.xz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/gmp/gmp-6.2.0.tar.xz ) && \
                                  echo "258e6cd51b3fbdfc185c716d55f82c08aff57df0c6fbd143cf6ed561267a1526  gmp-6.2.0.tar.xz" | sha256sum -c && \
                                  tar -xf gmp-6.2.0.tar.xz && mv gmp-6.2.0 gmp && rm gmp-6.2.0.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/mpfr-4.0.2.tar.xz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/mpfr/mpfr-4.0.2.tar.xz ) && \
                                  echo "1d3be708604eae0e42d578ba93b390c2a145f17743a744d8f3f8c2ad5855a38a  mpfr-4.0.2.tar.xz" | sha256sum -c && \
                                  tar -xf mpfr-4.0.2.tar.xz && mv mpfr-4.0.2 mpfr && rm mpfr-4.0.2.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/mpc-1.1.0.tar.gz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz ) && \
                                  echo "6985c538143c1208dcb1ac42cedad6ff52e267b47e5f970183a3e75125b43c2e  mpc-1.1.0.tar.gz" | sha256sum -c && \
                                  tar -xf mpc-1.1.0.tar.gz && mv mpc-1.1.0 mpc && rm mpc-1.1.0.tar.gz &&\
    cd /tmp/scratch/gcc-10.2.0 && \
      cat gcc/limitx.h gcc/glimits.h gcc/limity.h > `dirname $(x86_64-lfs-linux-gnu-gcc -print-libgcc-file-name)`/include-fixed/limits.h && \
    cd /tmp/scratch/gcc-10.2.0 && for file in gcc/config/{linux,i386/linux{,64}}.h; \
    do \
      echo ${file}; \
      cp -uv $file{,.orig}; \
      sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' -e 's@/usr@/tools@g' $file.orig > $file; \
      echo '#undef STANDARD_STARTFILE_PREFIX_1' >> $file; \
      echo '#undef STANDARD_STARTFILE_PREFIX_2' >> $file; \
      echo '#define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"' >> $file; \
      echo '#define STANDARD_STARTFILE_PREFIX_2 ""' >> $file; \
      touch $file.orig; \
    done && \
    cd /tmp/scratch/gcc-10.2.0 && sed -e '/m64=/s/lib64/lib/' -i.orig gcc/config/i386/t-linux64 && \
    hash -r && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CC=$LFS_TGT-gcc                                    \
                             CXX=$LFS_TGT-g++                                   \
                             AR=$LFS_TGT-ar                                     \
                             RANLIB=$LFS_TGT-ranlib                             \
                             ../gcc-10.2.0/configure \
                                --prefix=/tools                                \
                                --with-local-prefix=/tools                     \
                                --with-native-system-header-dir=/tools/include \
                                --enable-languages=c,c++,fortran               \
                                --disable-libgomp                              \
                                --disable-multilib && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gcc-10.2.0.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/gcc/gcc-10.2.0/gcc-10.2.0.tar.xz ) && \
                       echo "b8dd4368bb9c7f0b98188317ee0254dd8cc99d1e3a18d0ff146c855fe16c1d8c  gcc-10.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gcc-10.2.0.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gmp-6.2.0.tar.xz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/gmp/gmp-6.2.0.tar.xz ) && \
                                  echo "258e6cd51b3fbdfc185c716d55f82c08aff57df0c6fbd143cf6ed561267a1526  gmp-6.2.0.tar.xz" | sha256sum -c && \
                                  tar -xf gmp-6.2.0.tar.xz && mv gmp-6.2.0 gmp && rm gmp-6.2.0.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/mpfr-4.0.2.tar.xz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/mpfr/mpfr-4.0.2.tar.xz ) && \
                                  echo "1d3be708604eae0e42d578ba93b390c2a145f17743a744d8f3f8c2ad5855a38a  mpfr-4.0.2.tar.xz" | sha256sum -c && \
                                  tar -xf mpfr-4.0.2.tar.xz && mv mpfr-4.0.2 mpfr && rm mpfr-4.0.2.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/mpc-1.1.0.tar.gz || \
                                      wget -t 3 https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz ) && \
                                  echo "6985c538143c1208dcb1ac42cedad6ff52e267b47e5f970183a3e75125b43c2e  mpc-1.1.0.tar.gz" | sha256sum -c && \
                                  tar -xf mpc-1.1.0.tar.gz && mv mpc-1.1.0 mpc && rm mpc-1.1.0.tar.gz &&\
    cd /tmp/scratch/gcc-10.2.0 && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/isl-0.22.1.tar.xz || \
                                      wget -t 3 http://isl.gforge.inria.fr/isl-0.22.1.tar.xz ) && \
                                  echo "28658ce0f0bdb95b51fd2eb15df24211c53284f6ca2ac5e897acc3169e55b60f  isl-0.22.1.tar.xz" | sha256sum -c && \
                                  tar -xf isl-0.22.1.tar.xz && mv isl-0.22.1 isl && rm isl-0.22.1.tar.xz && \
    cd /tmp/scratch/gcc-10.2.0 && \
      cat gcc/limitx.h gcc/glimits.h gcc/limity.h > `dirname $(x86_64-lfs-linux-gnu-gcc -print-libgcc-file-name)`/include-fixed/limits.h && \
    cd /tmp/scratch/gcc-10.2.0 && for file in gcc/config/{linux,i386/linux{,64}}.h; \
    do \
      echo ${file}; \
      cp -uv $file{,.orig}; \
      sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' -e 's@/usr@/tools@g' $file.orig > $file; \
      echo '#undef STANDARD_STARTFILE_PREFIX_1' >> $file; \
      echo '#undef STANDARD_STARTFILE_PREFIX_2' >> $file; \
      echo '#define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"' >> $file; \
      echo '#define STANDARD_STARTFILE_PREFIX_2 ""' >> $file; \
      touch $file.orig; \
    done && \
    cd /tmp/scratch/gcc-10.2.0 && sed -e '/m64=/s/lib64/lib/' -i.orig gcc/config/i386/t-linux64 && \
    hash -r && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../gcc-10.2.0/configure \
                                --prefix=/tools                                \
                                --with-local-prefix=/tools                     \
                                --with-native-system-header-dir=/tools/include \
                                --enable-languages=c,c++,fortran               \
                                --disable-multilib \
                                --disable-bootstrap && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

#RUN false
RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/tar-1.32.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/tar/tar-1.32.tar.xz ) && \
                       echo "d0d3ae07f103323be809bc3eac0dcc386d52c5262499fe05511ac4788af1fdd8  tar-1.32.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf tar-1.32.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             FORCE_UNSAFE_CONFIGURE=1 \
                             ../tar-1.32/configure \
                                --host=$LFS_TGT \
                                --prefix=/tools && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xz-5.2.5.tar.gz || \
                         wget -t 3 https://tukaani.org/xz/xz-5.2.5.tar.gz ) && \
                       echo "f6f4910fd033078738bd82bfba4f49219d03b17eb0794eb91efbae419f4aba10  xz-5.2.5.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf xz-5.2.5.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../xz-5.2.5/configure \
                               --host=$LFS_TGT \
                               --prefix=/tools && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/coreutils-8.31.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/coreutils/coreutils-8.31.tar.xz ) && \
                       echo "ff7a9c918edce6b4f4b2725e3f9b37b0c4d193531cac49a48b56c4d0d3a9e9fd  coreutils-8.31.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf coreutils-8.31.tar.xz && \
    cd /tmp/scratch/coreutils-8.31 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      FORCE_UNSAFE_CONFIGURE=1 \
                                      ./configure \
                                        --host=$LFS_TGT \
                                        --prefix=/tools \
                                        --enable-install-program=hostname && \
                                        make -j$(nproc) && \
                                        make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/bash-5.0.tar.gz || \
                         wget -t 3 http://ftp.gnu.org/gnu/bash/bash-5.0.tar.gz ) && \
                       echo "b4a80f2ac66170b2913efbfb9f2594f1f76c7b1afd11f799e22035d63077fb4d  bash-5.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bash-5.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../bash-5.0/configure \
                              --host=$LFS_TGT \
                              --prefix=/tools \
                              --without-bash-malloc && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
                             ln -sv bash /tools/bin/sh && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/zlib-1.2.11.tar.gz || \
                         wget -t 3 https://www.zlib.net/zlib-1.2.11.tar.gz ) && \
                       echo "c3e5e9fdd5004dcb542feda5ee4f0ff0744628baf8ed2dd5d66f8ca1197cb1a1  zlib-1.2.11.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf zlib-1.2.11.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../zlib-1.2.11/configure \
                              --prefix=/tools && \
    cd /tmp/scratch/build && make -j$(nproc) && make -j$(nproc) install && \
    cp /tmp/scratch/zlib-1.2.11/zutil.h /tools/include/ && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gzip-1.10.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/gzip/gzip-1.10.tar.xz ) && \
                       echo "8425ccac99872d544d4310305f915f5ea81e04d0f437ef1a230dc9d1c819d7c0  gzip-1.10.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gzip-1.10.tar.xz && \
    cd /tmp/scratch/gzip-1.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                ./configure \
                                  --host=$LFS_TGT \
                                  --prefix=/tools && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/sed-4.8.tar.xz || \
                         wget -t 3 ftp://ftp.gnu.org/gnu/sed/sed-4.8.tar.xz ) && \
                       echo "f79b0cfea71b37a8eeec8490db6c5f7ae7719c35587f21edb0617f370eeff633  sed-4.8.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf sed-4.8.tar.xz && \
    cd /tmp/scratch/sed-4.8 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ./configure \
                                  --host=$LFS_TGT \
                                  --prefix=/tools && \
                               make && \
                               make install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/bzip2-1.0.8.tar.gz || \
                         wget -t 3 https://sourceware.org/pub/bzip2/bzip2-1.0.8.tar.gz ) && \
                       echo "ab5a03176ee106d3f0fa90e381da478ddae405918153cca248e682cd0c4a2269  bzip2-1.0.8.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bzip2-1.0.8.tar.gz && \
    cd /tmp/scratch/bzip2-1.0.8/ && sed -i 's@\(ln -s -f \)$(PREFIX)/bin/@\1@' Makefile && \
    cd /tmp/scratch/bzip2-1.0.8/ && sed -i 's@ -O2 @ -O3 -march=${IMAGE_ARCH} @' Makefile && \
    cd /tmp/scratch/bzip2-1.0.8/ && make -f Makefile-libbz2_so && \
                                    make clean && make && make PREFIX=/tools install && \
                                    cp -v bzip2-shared /tools/bin/bzip2 && \
                                    cp -av libbz2.so* /tools/lib && \
                                    ln -s libbz2.so.1.0 /tools/lib/libbz2.so && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/ncurses-6.2.tar.gz || \
                         wget -t 3 http://ftp.gnu.org/gnu/ncurses/ncurses-6.2.tar.gz ) && \
                       echo "30306e0c76e0f9f1f0de987cf1c82a5c21e1ce6568b9227f7da5b71cbea86c9d  ncurses-6.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf ncurses-6.2.tar.gz && \
    cd /tmp/scratch/ncurses-6.2 && sed -i s/mawk// configure && \
                                   CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   ./configure \
                                    --host=$LFS_TGT \
                                    --prefix=/tools \
                                    --with-shared   \
                                    --without-debug \
                                    --without-ada   \
                                    --enable-widec  \
                                    --enable-overwrite && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    ln -s libncursesw.so /tools/lib/libncurses.so

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/readline-8.0.tar.gz || \
                          wget -t 3 https://ftp.gnu.org/gnu/readline/readline-8.0.tar.gz ) && \
                        echo "e339f51971478d369f8a053a330a190781acb9864cf4c541060f12078948e461  readline-8.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xf readline-8.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../readline-8.0/configure \
                               --host=$LFS_TGT \
                               --prefix=/tools \
                               --disable-static && \
                             make -j$(nproc) SHLIB_LIBS="-L/tools/lib -lncurses" && \
                             make install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/pcre-8.44.tar.gz || \
                         wget -t 3 https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz ) && \
                       echo "aecafd4af3bd0f3935721af77b889d9024b2e01d96b58471bd91a3063fb47728  pcre-8.44.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf pcre-8.44.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               ../pcre-8.44/configure \
                                --host=$LFS_TGT \
                                --prefix=/tools \
                                --enable-unicode-properties       \
                                --enable-pcre16                   \
                                --enable-pcre32                   \
                                --enable-pcregrep-libz            \
                                --enable-pcregrep-libbz2          \
                                --enable-pcretest-libreadline     \
                                --disable-static && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/grep-3.4.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/grep/grep-3.4.tar.xz ) && \
                       echo "58e6751c41a7c25bfc6e9363a41786cff3ba5709cf11d5ad903cf7cce31cc3fb  grep-3.4.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf grep-3.4.tar.xz && \
    cd /tmp/scratch/grep-3.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                ./configure \
                                  --host=$LFS_TGT \
                                  --prefix=/tools && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/perl-5.32.0.tar.gz || \
                          wget -t 3 https://www.cpan.org/src/5.0/perl-5.32.0.tar.gz ) && \
                        echo "efeb1ce1f10824190ad1cadbcccf6fdb8a5d37007d0100d2d9ae5f2b5900c0b4  perl-5.32.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xf perl-5.32.0.tar.gz && \
    cd /tmp/scratch/perl-5.32.0/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./configure.gnu \
                                      --prefix=/tools && \
    cd /tmp/scratch/perl-5.32.0/ && make -j$(nproc) && \
                                    make install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gawk-5.1.0.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/gawk/gawk-5.1.0.tar.xz ) && \
                       echo "cf5fea4ac5665fd5171af4716baab2effc76306a9572988d5ba1078f196382bd  gawk-5.1.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gawk-5.1.0.tar.xz && \
    cd /tmp/scratch/gawk-5.1.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --host=$LFS_TGT \
                                        --prefix=/tools && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/make-4.3.tar.gz || \
                         wget -t 3 http://ftp.gnu.org/gnu/make/make-4.3.tar.gz ) && \
                       echo "e05fdde47c5f7ca45cb697e973894ff4f5d79e13b750ed57d7b66d8defc78e19  make-4.3.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf make-4.3.tar.gz && \
    cd /tmp/scratch/make-4.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                ./configure \
                                  --prefix=/tools \
                                  --without-guile && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/openssl-1.1.1g.tar.gz || \
                          wget -t 3 https://www.openssl.org/source/openssl-1.1.1g.tar.gz ) && \
                        echo "ddb04774f1e32f0c49751e21b67216ac87852ceb056b75209af2443400636d46  openssl-1.1.1g.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf openssl-1.1.1g.tar.gz && \
    cd /tmp/scratch/openssl-1.1.1g && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./config \
                                       --prefix=/tools \
                                       --libdir=lib shared && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install_sw && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/wget-1.20.3.tar.gz || \
                         wget -t 3 https://ftp.gnu.org/gnu/wget/wget-1.20.3.tar.gz ) && \
                       echo "31cccfc6630528db1c8e3a06f6decf2a370060b982841cfab2b8677400a5092e  wget-1.20.3.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf wget-1.20.3.tar.gz && \
    cd /tmp/scratch/wget-1.20.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   LDFLAGS="-L/tools/lib/" \
                                   PKG_CONFIG_PATH="/tools/lib/pkgconfig/" \
                                   ./configure \
                                    --prefix=/tools \
                                    --sysconfdir=/etc \
                                    --with-ssl=openssl && \
                                   make -j$(nproc) && \
                                   make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tools/ssl/certs/ && cp -r /etc/ssl/certs/* /tools/ssl/certs/

RUN ln -s gcc /tools/bin/cc && \
    rm /tools/bin/$LFS_TGT* && \
    rm -rf /bin && /tools/bin/ln -s /tools/bin / && \
    rm -rf /usr/bin && /tools/bin/ln -s /tools/bin /usr/ && \
    rm -rf /lib && /tools/bin/ln -s /tools/lib /

ENV PKG_CONFIG_PATH /tools/lib/pkgconfig/
ENV PATH /tools/bin/
ENV LD_LIBRARY_PATH /tools/lib/
ENV SSL_CERT_DIR /tools/ssl/certs/
ENV SSL_CERT_FILE /tools/ssl/certs/ca-certificates.crt

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/which-2.21.tar.gz || \
                         wget -t 3 ftp://ftp.gnu.org/gnu/which/which-2.21.tar.gz ) && \
                       echo "f4a245b94124b377d8b49646bf421f9155d36aa7614b6ebf83705d3ffc76eaad  which-2.21.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf which-2.21.tar.gz && \
    cd /tmp/scratch/which-2.21 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  ./configure \
                                     --prefix=/tools && \
                                  make -j$(nproc) && \
                                  make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/patch-2.7.6.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/patch/patch-2.7.6.tar.xz ) && \
                       echo "ac610bda97abe0d9f6b7c963255a11dcb196c25e337c61f94e4778d632f1d8fd  patch-2.7.6.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf patch-2.7.6.tar.xz && \
    cd /tmp/scratch/patch-2.7.6 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   ./configure \
                                     --prefix=/tools && \
                                   make -j$(nproc) && \
                                   make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/diffutils-3.7.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/diffutils/diffutils-3.7.tar.xz ) && \
                       echo "b3a7a6221c3dc916085f0d205abf6b8e1ba443d4dd965118da364a1dc1cb3a26  diffutils-3.7.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf diffutils-3.7.tar.xz && \
    cd /tmp/scratch/diffutils-3.7 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/tools && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/file-5.38.tar.gz || \
                         wget -t 3 ftp://ftp.astron.com/pub/file/file-5.38.tar.gz ) && \
                       echo "593c2ffc2ab349c5aea0f55fedfe4d681737b6b62376a9b3ad1e77b2cc19fa34  file-5.38.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf file-5.38.tar.gz && \
    cd /tmp/scratch/file-5.38 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/tools && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/m4-1.4.18.tar.gz || \
                         wget -t 3 http://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.gz ) && \
                       echo "ab2633921a5cd38e48797bf5521ad259bdc4b979078034a3b790d7fec5493fab  m4-1.4.18.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf m4-1.4.18.tar.gz && \
    cd /tmp/scratch/m4-1.4.18 && sed -i 's/IO_ftrylockfile/IO_EOF_SEEN/' lib/*.c && \
                                 echo "#define _IO_IN_BACKUP 0x100" >> lib/stdio-impl.h && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../m4-1.4.18/configure \
                               --prefix=/tools && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch/ && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/perl-5.32.0.tar.gz || \
                          wget -t 3 https://www.cpan.org/src/5.0/perl-5.32.0.tar.gz ) && \
                        echo "efeb1ce1f10824190ad1cadbcccf6fdb8a5d37007d0100d2d9ae5f2b5900c0b4  perl-5.32.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch/ && tar -xf perl-5.32.0.tar.gz && \
    cd /tmp/scratch/perl-5.32.0/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./configure.gnu \
                                      --prefix=/tools && \
    cd /tmp/scratch/perl-5.32.0/ && make -j$(nproc) && \
                                    make install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libtool-2.4.6.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/libtool/libtool-2.4.6.tar.xz ) && \
                       echo "7c87a8c2c8c0fc9cd5019e402bed4292462d00a718a7cd5f11218153bf28b26f  libtool-2.4.6.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf libtool-2.4.6.tar.xz && \
    cd /tmp/scratch/libtool-2.4.6 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                ./configure \
                                  --prefix=/tools && \
                                make -j$(nproc) && \
                                make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/findutils-4.7.0.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/findutils/findutils-4.7.0.tar.xz ) && \
                       echo "c5fefbdf9858f7e4feb86f036e1247a54c79fc2d8e4b7064d5aaa1f47dfa789a  findutils-4.7.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf findutils-4.7.0.tar.xz && \
    cd /tmp/scratch/findutils-4.7.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/tools && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/autoconf-2.69.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.xz ) && \
                       echo "64ebcec9f8ac5b2487125a86a7760d2591ac9e1d3dbd59489633f9de62a57684  autoconf-2.69.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf autoconf-2.69.tar.xz && \
    cd /tmp/scratch/autoconf-2.69 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                     ./configure \
                                      --prefix=/tools && \
                                     make -j$(nproc) && \
                                     make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/automake-1.16.2.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/automake/automake-1.16.2.tar.xz ) && \
                       echo "ccc459de3d710e066ab9e12d2f119bd164a08c9341ca24ba22c9adaa179eedd0  automake-1.16.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf automake-1.16.2.tar.xz && \
    cd /tmp/scratch/automake-1.16.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       ./configure \
                                        --prefix=/tools && \
                                       make -j$(nproc) && \
                                       make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN rm -rf /var/lib/apt/lists/*

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

RUN echo $'passwd: files \n\
group: files \n\
shadow: files \n\
hosts: files dns \n\
networks: files \n\
protocols: files \n\
services: files \n\
ethers: files \n\
rpc: files \n\
' > /etc/nsswitch.conf

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gmp-6.2.0.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/gmp/gmp-6.2.0.tar.xz ) && \
                       echo "258e6cd51b3fbdfc185c716d55f82c08aff57df0c6fbd143cf6ed561267a1526  gmp-6.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gmp-6.2.0.tar.xz && \
    cd /tmp/scratch/gmp-6.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 ./configure \
                                 --prefix=/tools && \
                                 make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/coreutils-8.31.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/coreutils/coreutils-8.31.tar.xz ) && \
                       echo "ff7a9c918edce6b4f4b2725e3f9b37b0c4d193531cac49a48b56c4d0d3a9e9fd  coreutils-8.31.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf coreutils-8.31.tar.xz && \
    cd /tmp/scratch/coreutils-8.31 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      FORCE_UNSAFE_CONFIGURE=1 \
                                      ./configure \
                                        --prefix=/tools \
                                        --enable-install-program=hostname && \
                                        make -j$(nproc) && \
                                        make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/bison-3.6.2.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/bison/bison-3.6.2.tar.xz ) && \
                       echo "4a164b5cc971b896ce976bf4b624fab7279e0729cf983a5135df7e4df0970f6e  bison-3.6.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bison-3.6.2.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../bison-3.6.2/configure \
                             --prefix=/tools && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

#RUN which expr && false

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gmp-6.2.0.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/gmp/gmp-6.2.0.tar.xz ) && \
                       echo "258e6cd51b3fbdfc185c716d55f82c08aff57df0c6fbd143cf6ed561267a1526  gmp-6.2.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gmp-6.2.0.tar.xz && \
    cd /tmp/scratch/gmp-6.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                 ./configure \
                                 --prefix=/tools && \
                                 make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gettext-0.20.2.tar.xz || \
                         wget -t 3 http://ftp.gnu.org/gnu/gettext/gettext-0.20.2.tar.xz ) && \
                       echo "b22b818e644c37f6e3d1643a1943c32c3a9bff726d601e53047d2682019ceaba  gettext-0.20.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf gettext-0.20.2.tar.xz && \
    cd /tmp/scratch/gettext-0.20.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                        --prefix=/tools \
                                        --disable-shared && \
                                      make -j$(nproc) && \
                                      make -j$(nproc) install && \
                                      cp -v gettext-tools/src/{msgfmt,msgmerge,xgettext} /tools/bin && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/tcl8.6.10-src.tar.gz || \
                         wget -t 3 https://prdownloads.sourceforge.net/tcl/tcl8.6.10-src.tar.gz ) && \
                       echo "5196dbf6638e3df8d5c87b5815c8c2b758496eb6f0e41446596c9a4e638d87ed  tcl8.6.10-src.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf tcl8.6.10-src.tar.gz && \
    cd /tmp/scratch/tcl8.6.10/unix/ && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       ./configure \
                                         --prefix=/tools && \
                                       make -j$(nproc) && \
                                       make -j$(nproc) install && \
                                       chmod -v u+w /tools/lib/libtcl8.6.so && \
                                       make install-private-headers && \
                                       ln -sv tclsh8.6 /tools/bin/tclsh && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/expect5.45.4.tar.gz || \
                         wget -t 3 https://prdownloads.sourceforge.net/expect/expect5.45.4.tar.gz ) && \
                       echo "49a7da83b0bdd9f46d04a04deec19c7767bb9a323e40c4781f89caf760b92c34  expect5.45.4.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf expect5.45.4.tar.gz && \
    cd /tmp/scratch/expect5.45.4 && cp -v configure{,.orig} && \
                                    sed 's:/usr/local/bin:/bin:' configure.orig > configure && \
                                    CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./configure \
                                    --with-tcl=/tools/lib \
                                    --with-tclinclude=/tools/include && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) SCRIPTS="" install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/dejagnu-1.6.2.tar.gz || \
                         wget -t 3 https://ftp.gnu.org/gnu/dejagnu/dejagnu-1.6.2.tar.gz ) && \
                       echo "0d0671e1b45189c5fc8ade4b3b01635fb9eeab45cf54f57db23e4c4c1a17d261  dejagnu-1.6.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf dejagnu-1.6.2.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../dejagnu-1.6.2/configure \
                               --prefix=/tools && \
                             make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch


RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/bash-5.0.tar.gz || \
                         wget -t 3 http://ftp.gnu.org/gnu/bash/bash-5.0.tar.gz ) && \
                       echo "b4a80f2ac66170b2913efbfb9f2594f1f76c7b1afd11f799e22035d63077fb4d  bash-5.0.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf bash-5.0.tar.gz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                             ../bash-5.0/configure \
                              --prefix=/tools \
                              --without-bash-malloc && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    rm -f /tools/bin/sh && ln -sv bash /tools/bin/sh 

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/tar-1.32.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/tar/tar-1.32.tar.xz ) && \
                       echo "d0d3ae07f103323be809bc3eac0dcc386d52c5262499fe05511ac4788af1fdd8  tar-1.32.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf tar-1.32.tar.xz && \
    cd /tmp/scratch/tar-1.32 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                FORCE_UNSAFE_CONFIGURE=1 \
                                ./configure \
                                  --prefix=/tools && \
                                make && \
                                make install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/inetutils-1.9.4.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/inetutils/inetutils-1.9.4.tar.xz ) && \
                       echo "849d96f136effdef69548a940e3e0ec0624fc0c81265296987986a0dd36ded37  inetutils-1.9.4.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf inetutils-1.9.4.tar.xz && \
    cd /tmp/scratch/inetutils-1.9.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./configure \
                                    --prefix=/tools \
                                    --disable-logger     \
                                    --disable-whois      \
                                    --disable-rcp        \
                                    --disable-rexec      \
                                    --disable-rlogin     \
                                    --disable-rsh        \
                                    --disable-servers && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/texinfo-6.7.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/texinfo/texinfo-6.7.tar.xz ) && \
                       echo "988403c1542d15ad044600b909997ba3079b10e03224c61188117f3676b02caa  texinfo-6.7.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf texinfo-6.7.tar.xz && \
    cd /tmp/scratch/texinfo-6.7 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                   ./configure \
                                   --prefix=/tools \
                                   --disable-static && \
                                     make -j$(nproc) && \
                                     make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libffi-3.3.tar.gz || \
                         wget -t 3 https://gcc.gnu.org/pub/libffi/libffi-3.3.tar.gz ) && \
                       echo "72fba7922703ddfa7a028d513ac15a85c8d54c8d67f55fa5a4802885dc652056  libffi-3.3.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf libffi-3.3.tar.gz && \
    cd /tmp/scratch/libffi-3.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  ./configure \
                                  --prefix=/tools \
                                  --enable-shared && \
                                  make -j$(nproc) && \
                                  make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/Python-3.8.4.tar.xz || \
                         wget -t 3 https://www.python.org/ftp/python/3.8.4/Python-3.8.4.tar.xz ) && \
                       echo "5f41968a95afe9bc12192d7e6861aab31e80a46c46fa59d3d837def6a4cd4d37  Python-3.8.4.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf Python-3.8.4.tar.xz && \
    cd /tmp/scratch/Python-3.8.4/ && sed -i "s/Werror=implicit-function-declaration/Wno-error/g" configure && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/ncursesw/" \
                             ../Python-3.8.4/configure \
                             --prefix=/tools \
                             --with-openssl=/tools \
                             --enable-shared && \
                             make -j$(nproc) && \
                             make -j$(nproc) install && \
    ln -s python3 /tools/bin/python && \
    python -m pip install --force-reinstall pip==20.1.1 && \
    cd / && rm -rf /tmp/scratch

RUN python -m pip install meson

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/ninja-1.10.1.tar.gz || \
                         wget -t 3 https://github.com/ninja-build/ninja/archive/v1.10.1.tar.gz -O ninja-1.10.1.tar.gz ) && \
                       echo "a6b6f7ac360d4aabd54e299cc1d8fa7b234cd81b9401693da21221c62569a23e  ninja-1.10.1.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf ninja-1.10.1.tar.gz && \
    cd /tmp/scratch/ninja-1.10.1/ && ./configure.py --bootstrap && ./ninja && mv ninja /tools/bin/ && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/pkg-config-0.29.2.tar.gz || \
                         wget -t 3 https://pkgconfig.freedesktop.org/releases/pkg-config-0.29.2.tar.gz ) && \
                       echo "6fc69c01688c9458a57eb9a1664c9aba372ccda420a02bf4429fe610e7e7d591  pkg-config-0.29.2.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf pkg-config-0.29.2.tar.gz && \
    cd /tmp/scratch/pkg-config-0.29.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                         ./configure \
                                         --prefix=/tools \
                                         --with-internal-glib && \
                                         make -j$(nproc) && \
                                         make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/shadow-4.1.4.tar.gz || \
                         wget -t 3 https://src.fedoraproject.org/repo/pkgs/shadow-utils/shadow-4.1.4.tar.gz/e1072df927bfb4410ee4dfe26dd81a17/shadow-4.1.4.tar.gz ) && \
                       echo "7e38a7826f6e71e89b55669e8343af05ae33ecfba99aad178cad45845d950a93  shadow-4.1.4.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf shadow-4.1.4.tar.gz && \
    cd /tmp/scratch/shadow-4.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./configure \
                                    --prefix=/tools \
                                    --exec-prefix=/tools && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/freetype-2.10.2.tar.xz || \
                         wget -t 3 https://downloads.sourceforge.net/freetype/freetype-2.10.2.tar.xz ) && \
                       echo "1543d61025d2e6312e0a1c563652555f17378a204a61e99928c9fcef030a2d8b  freetype-2.10.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf freetype-2.10.2.tar.xz && \
    cd /tmp/scratch/freetype-2.10.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/util-linux-2.35.1.tar.xz || \
                         wget -t 3 https://www.kernel.org/pub/linux/utils/util-linux/v2.35/util-linux-2.35.1.tar.xz ) && \
                       echo "d9de3edd287366cd908e77677514b9387b22bc7b88f45b83e1922c3597f1d7f9  util-linux-2.35.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf util-linux-2.35.1.tar.xz && \
    cd /tmp/scratch/util-linux-2.35.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                      --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      --disable-docs \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tools/ssl/certs/ && cp /etc/ssl/certs/ca-certificates.crt /tools/ssl/certs/ && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/curl-7.70.0.tar.xz || \
                         wget -t 3 https://curl.haxx.se/download/curl-7.70.0.tar.xz ) && \
                       echo "032f43f2674008c761af19bf536374128c16241fb234699a55f9fb603fcfbae7  curl-7.70.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf curl-7.70.0.tar.xz && \
    cd /tmp/scratch/curl-7.70.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" ./configure \
                                     --prefix=/tools \
                                     --disable-static \
                                     --with-ssl=/tools \
                                     --with-ca-bundle=/tools/ssl/certs/ca-certificates.crt && \
                                   make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && true


RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/git-2.26.2.tar.xz || \
                         wget -t 3 https://www.kernel.org/pub/software/scm/git/git-2.26.2.tar.xz ) && \
                       echo "6d65132471df9e531807cb2746f8be317e22a343b9385bbe11c9ce7f0d2fc848  git-2.26.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf git-2.26.2.tar.xz && \
    cd /tmp/scratch/git-2.26.2 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/" \
                                  CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                  LDFLAGS="-L/tools/lib/" \
                                  ./configure \
                                   --prefix=/tools \
                                   --with-curl=/tools \
                                   --with-openssl=/tools && \
                                 make -j$(nproc) && \
                                 make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tools/etc && \
    git config --system http.sslCAinfo /tools/ssl/certs/ca-certificates.crt

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/cmake-3.18.3.tar.gz || \
                         wget -t 3 https://github.com/Kitware/CMake/releases/download/v3.18.3/cmake-3.18.3.tar.gz ) && \
                       echo "2c89f4e30af4914fd6fb5d00f863629812ada848eee4e2d29ec7e456d7fa32e5  cmake-3.18.3.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf cmake-3.18.3.tar.gz && \
    cd /tmp/scratch/cmake-3.18.3 && CC=gcc \
                                    CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    ./bootstrap --prefix=/tools && \
                                    CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                    make -j $(nproc) && \
                                    make install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/unzip60.tar.gz || \
                         wget -t 3 wget https://downloads.sourceforge.net/infozip/unzip60.tar.gz || true ) && \
                       echo "036d96991646d0449ed0aa952e4fbe21b476ce994abc276e49d30e686708bd37  unzip60.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf unzip60.tar.gz && \
    cd /tmp/scratch/unzip60 && make -f unix/Makefile unzips CC=gcc LD=gcc CFLAGS="-O3 -march=${IMAGE_ARCH} -Wall" && \
    cd /tmp/scratch/unzip60 && make prefix=/tools -f unix/Makefile install && \
    cd / && rm -rf /tmp/scratch

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/zip30.tgz || \
                         wget -t 3 ftp://ftp.info-zip.org/pub/infozip/src/zip30.tgz ) && \
                       echo "f0e8bb1f9b7eb0b01285495a2699df3a4b766784c1765a8f1aeedf63c0806369  zip30.tgz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf zip30.tgz && \
    cd /tmp/scratch/zip30 && make -f unix/Makefile generic_gcc && \
    cd /tmp/scratch/zip30 && make prefix=/tools -f unix/Makefile install && \
    rm -rf /tools/man && \
    cd / && rm -rf /tmp/scratch

RUN rm -f /tools/bin/x86_64-* && \
    rm -rf /tools/lib/*.la && \
    rm -rf /tools/lib/pkgconfig && \
    strip --strip-debug /tools/lib/* && \
    /usr/bin/strip --strip-unneeded /tools/{,s}bin/* || true && \
    rm -rf /tools/{,share}/{info,man,doc} && \
    find /tools/{lib,libexec} -name \*.la -delete

FROM debian:stretch-slim AS bootstrap_java

RUN apt update -y && \
    apt install -y \
     curl \
     && \
    apt autoremove -y && \
    apt clean && \
	rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    ESUM='1285da6278f2d38a790a21148d7e683f20de0799c44b937043830ef6b57f58c4'; \
    BINARY_URL='https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u265-b01/OpenJDK8U-jdk_x64_linux_hotspot_8u265b01.tar.gz'; \
    curl -LfsSo /tmp/openjdk.tar.gz ${BINARY_URL}; \
    echo "${ESUM} */tmp/openjdk.tar.gz" | sha256sum -c -; \
    mkdir -p /tools/java/openjdk; \
    cd /tools/java/openjdk; \
    tar -xf /tmp/openjdk.tar.gz --strip-components=1; \
    rm -rf /tmp/openjdk.tar.gz;

FROM tools AS build_openjdk

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

ENV PATH /tools/bin/:${PATH}
ENV PKG_CONFIG_PATH /tools/pkgconfig/:/tools/lib64/:/tools/lib64/pkgconfig/:/tools/lib/:/tools/lib/pkgconfig/:/tools/share/pkgconfig/
ENV LD_LIBRARY_PATH /tools/lib64/:/tools/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/util-macros-1.19.2.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/util/util-macros-1.19.2.tar.bz2 ) && \
                       echo "d7e43376ad220411499a79735020f9d145fdc159284867e99467e0d771f3e712  util-macros-1.19.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf util-macros-1.19.2.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                            ../util-macros-1.19.2/configure --prefix=/tools \
                                            --sysconfdir=/tools \
                                            --localstatedir=/var \
                                             && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xorgproto-2020.1.tar.bz2 || \
                         wget -t 3 https://xorg.freedesktop.org/archive/individual/proto/xorgproto-2020.1.tar.bz2 ) && \
                       echo "54a153f139035a376c075845dd058049177212da94d7a9707cf9468367b699d2  xorgproto-2020.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xorgproto-2020.1.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                        meson --prefix=/tools ../xorgproto-2020.1 && \
                             ninja && ninja install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXau-1.0.9.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXau-1.0.9.tar.bz2 ) && \
                       echo "ccf8cbf0dbf676faa2ea0a6d64bcc3b6746064722b606c8c52917ed00dcb73ec  libXau-1.0.9.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXau-1.0.9.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../libXau-1.0.9/configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXdmcp-1.1.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXdmcp-1.1.3.tar.bz2 ) && \
                       echo "20523b44aaa513e17c009e873ad7bbc301507a3224c232610ce2e099011c6529  libXdmcp-1.1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXdmcp-1.1.3.tar.bz2 && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../libXdmcp-1.1.3/configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-proto-1.14.tar.xz || \
                         wget -t 3 https://xorg.freedesktop.org/archive/individual/proto/xcb-proto-1.14.tar.xz ) && \
                       echo "186a3ceb26f9b4a015f5a44dcc814c93033a5fc39684f36f1ecc79834416a605  xcb-proto-1.14.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-proto-1.14.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../xcb-proto-1.14/configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libxcb-1.14.tar.xz || \
                         wget -t 3 https://xorg.freedesktop.org/archive/individual/lib/libxcb-1.14.tar.xz ) && \
                       echo "a55ed6db98d43469801262d81dc2572ed124edc3db31059d4e9916eb9f844c34  libxcb-1.14.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxcb-1.14.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CPPFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ../libxcb-1.14/configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xtrans-1.4.0.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/xtrans-1.4.0.tar.bz2 ) && \
                       echo "377c4491593c417946efcd2c7600d1e62639f7a8bbca391887e2c4679807d773  xtrans-1.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xtrans-1.4.0.tar.bz2 && \
    cd /tmp/scratch/xtrans-1.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libX11-1.6.9.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libX11-1.6.9.tar.bz2 ) && \
                       echo "9cc7e8d000d6193fa5af580d50d689380b8287052270f5bb26a5fb6b58b2bed1  libX11-1.6.9.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libX11-1.6.9.tar.bz2 && \
    cd /tmp/scratch/libX11-1.6.9 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXext-1.3.4.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXext-1.3.4.tar.bz2 ) && \
                       echo "59ad6fcce98deaecc14d39a672cf218ca37aba617c9a0f691cac3bcd28edf82b  libXext-1.3.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXext-1.3.4.tar.bz2 && \
    cd /tmp/scratch/libXext-1.3.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libFS-1.0.8.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libFS-1.0.8.tar.bz2 ) && \
                       echo "c8e13727149b2ddfe40912027459b2522042e3844c5cd228c3300fe5eef6bd0f  libFS-1.0.8.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libFS-1.0.8.tar.bz2 && \
    cd /tmp/scratch/libFS-1.0.8 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libICE-1.0.10.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libICE-1.0.10.tar.bz2 ) && \
                       echo "6f86dce12cf4bcaf5c37dddd8b1b64ed2ddf1ef7b218f22b9942595fb747c348  libICE-1.0.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libICE-1.0.10.tar.bz2 && \
    cd /tmp/scratch/libICE-1.0.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libSM-1.2.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libSM-1.2.3.tar.bz2 ) && \
                       echo "2d264499dcb05f56438dee12a1b4b71d76736ce7ba7aa6efbf15ebb113769cbb  libSM-1.2.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libSM-1.2.3.tar.bz2 && \
    cd /tmp/scratch/libSM-1.2.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXScrnSaver-1.2.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXScrnSaver-1.2.3.tar.bz2 ) && \
                       echo "f917075a1b7b5a38d67a8b0238eaab14acd2557679835b154cf2bca576e89bf8  libXScrnSaver-1.2.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXScrnSaver-1.2.3.tar.bz2 && \
    cd /tmp/scratch/libXScrnSaver-1.2.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXt-1.2.0.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXt-1.2.0.tar.bz2 ) && \
                       echo "b31df531dabed9f4611fc8980bc51d7782967e2aff44c4105251a1acb5a77831  libXt-1.2.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXt-1.2.0.tar.bz2 && \
    cd /tmp/scratch/libXt-1.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXmu-1.1.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXmu-1.1.3.tar.bz2 ) && \
                       echo "9c343225e7c3dc0904f2122b562278da5fed639b1b5e880d25111561bac5b731  libXmu-1.1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXmu-1.1.3.tar.bz2 && \
    cd /tmp/scratch/libXmu-1.1.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gperf-3.1.tar.gz || \
                         wget -t 3 https://ftp.gnu.org/gnu/gperf/gperf-3.1.tar.gz ) && \
                       echo "588546b945bba4b70b6a3a616e80b4ab466e3f33024a352fc2198112cdbb3ae2  gperf-3.1.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf gperf-3.1.tar.gz && \
    cd /tmp/scratch/gperf-3.1 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/gettext-0.20.2.tar.xz || \
                         wget -t 3 https://ftp.gnu.org/gnu/gettext/gettext-0.20.2.tar.xz ) && \
                       echo "b22b818e644c37f6e3d1643a1943c32c3a9bff726d601e53047d2682019ceaba  gettext-0.20.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf gettext-0.20.2.tar.xz && \
    cd /tmp/scratch/gettext-0.20.2 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/freetype-2.10.2.tar.xz || \
                         wget -t 3 https://downloads.sourceforge.net/freetype/freetype-2.10.2.tar.xz ) && \
                       echo "1543d61025d2e6312e0a1c563652555f17378a204a61e99928c9fcef030a2d8b  freetype-2.10.2.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf freetype-2.10.2.tar.xz && \
    cd /tmp/scratch/freetype-2.10.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                       CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/util-linux-2.35.1.tar.xz || \
                         wget -t 3 https://www.kernel.org/pub/linux/utils/util-linux/v2.35/util-linux-2.35.1.tar.xz ) && \
                       echo "d9de3edd287366cd908e77677514b9387b22bc7b88f45b83e1922c3597f1d7f9  util-linux-2.35.1.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf util-linux-2.35.1.tar.xz && \
    cd /tmp/scratch/util-linux-2.35.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                       --prefix=/tools \
                                       --disable-pylibmount \
                                       --disable-static     \
                                       --without-python     \
                                       --without-systemd    \
                                       --without-systemdsystemunitdir && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libxml2-2.9.10.tar.gz || \
                         wget -t 3 http://xmlsoft.org/sources/libxml2-2.9.10.tar.gz ) && \
                       echo "aafee193ffb8fe0c82d4afef6ef91972cbaf5feea100edc2f262750611b4be1f  libxml2-2.9.10.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxml2-2.9.10.tar.gz && \
    cd /tmp/scratch/libxml2-2.9.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure \
                                      --prefix=/tools && \
                                    make -j$(nproc) && \
                                    make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/fontconfig-2.13.1.tar.bz2 || \
                         wget -t 3 https://www.freedesktop.org/software/fontconfig/release/fontconfig-2.13.1.tar.bz2 ) && \
                       echo "f655dd2a986d7aa97e052261b36aa67b0a64989496361eca8d604e6414006741  fontconfig-2.13.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf fontconfig-2.13.1.tar.bz2 && \
    cd /tmp/scratch/fontconfig-2.13.1 && CFLAGS="-O3 -march=${IMAGE_ARCH} -I/tools/include/" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      --disable-static \
                                      --disable-docs \
                                      --enable-libxml2 \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXpm-3.5.13.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXpm-3.5.13.tar.bz2 ) && \
                       echo "9cd1da57588b6cb71450eff2273ef6b657537a9ac4d02d0014228845b935ac25  libXpm-3.5.13.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXpm-3.5.13.tar.bz2 && \
    cd /tmp/scratch/libXpm-3.5.13 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXaw-1.0.13.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXaw-1.0.13.tar.bz2 ) && \
                       echo "8ef8067312571292ccc2bbe94c41109dcf022ea5a4ec71656a83d8cce9edb0cd  libXaw-1.0.13.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXaw-1.0.13.tar.bz2 && \
    cd /tmp/scratch/libXaw-1.0.13 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXfixes-5.0.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXfixes-5.0.3.tar.bz2 ) && \
                       echo "de1cd33aff226e08cefd0e6759341c2c8e8c9faf8ce9ac6ec38d43e287b22ad6  libXfixes-5.0.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXfixes-5.0.3.tar.bz2 && \
    cd /tmp/scratch/libXfixes-5.0.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXcomposite-0.4.5.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXcomposite-0.4.5.tar.bz2 ) && \
                       echo "b3218a2c15bab8035d16810df5b8251ffc7132ff3aa70651a1fba0bfe9634e8f  libXcomposite-0.4.5.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXcomposite-0.4.5.tar.bz2 && \
    cd /tmp/scratch/libXcomposite-0.4.5 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXrender-0.9.10.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXrender-0.9.10.tar.bz2 ) && \
                       echo "c06d5979f86e64cabbde57c223938db0b939dff49fdb5a793a1d3d0396650949  libXrender-0.9.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXrender-0.9.10.tar.bz2 && \
    cd /tmp/scratch/libXrender-0.9.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXcursor-1.2.0.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXcursor-1.2.0.tar.bz2 ) && \
                       echo "3ad3e9f8251094af6fe8cb4afcf63e28df504d46bfa5a5529db74a505d628782  libXcursor-1.2.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXcursor-1.2.0.tar.bz2 && \
    cd /tmp/scratch/libXcursor-1.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXdamage-1.1.5.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXdamage-1.1.5.tar.bz2 ) && \
                       echo "b734068643cac3b5f3d2c8279dd366b5bf28c7219d9e9d8717e1383995e0ea45  libXdamage-1.1.5.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXdamage-1.1.5.tar.bz2 && \
    cd /tmp/scratch/libXdamage-1.1.5 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libfontenc-1.1.4.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libfontenc-1.1.4.tar.bz2 ) && \
                       echo "2cfcce810ddd48f2e5dc658d28c1808e86dcf303eaff16728b9aa3dbc0092079  libfontenc-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libfontenc-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libfontenc-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXfont2-2.0.4.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXfont2-2.0.4.tar.bz2 ) && \
                       echo "6d151b3368e5035efede4b6264c0fdc6662c1c99dbc2de425e3480cababc69e6  libXfont2-2.0.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXfont2-2.0.4.tar.bz2 && \
    cd /tmp/scratch/libXfont2-2.0.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXft-2.3.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXft-2.3.3.tar.bz2 ) && \
                       echo "225c68e616dd29dbb27809e45e9eadf18e4d74c50be43020ef20015274529216  libXft-2.3.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXft-2.3.3.tar.bz2 && \
    cd /tmp/scratch/libXft-2.3.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXi-1.7.10.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXi-1.7.10.tar.bz2 ) && \
                       echo "36a30d8f6383a72e7ce060298b4b181fd298bc3a135c8e201b7ca847f5f81061  libXi-1.7.10.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXi-1.7.10.tar.bz2 && \
    cd /tmp/scratch/libXi-1.7.10 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXinerama-1.1.4.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXinerama-1.1.4.tar.bz2 ) && \
                       echo "0008dbd7ecf717e1e507eed1856ab0d9cf946d03201b85d5dcf61489bb02d720  libXinerama-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXinerama-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libXinerama-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXrandr-1.5.2.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXrandr-1.5.2.tar.bz2 ) && \
                       echo "8aea0ebe403d62330bb741ed595b53741acf45033d3bda1792f1d4cc3daee023  libXrandr-1.5.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXrandr-1.5.2.tar.bz2 && \
    cd /tmp/scratch/libXrandr-1.5.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXres-1.2.0.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXres-1.2.0.tar.bz2 ) && \
                       echo "ff75c1643488e64a7cfbced27486f0f944801319c84c18d3bd3da6bf28c812d4  libXres-1.2.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXres-1.2.0.tar.bz2 && \
    cd /tmp/scratch/libXres-1.2.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXtst-1.2.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXtst-1.2.3.tar.bz2 ) && \
                       echo "4655498a1b8e844e3d6f21f3b2c4e2b571effb5fd83199d428a6ba7ea4bf5204  libXtst-1.2.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXtst-1.2.3.tar.bz2 && \
    cd /tmp/scratch/libXtst-1.2.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXv-1.0.11.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXv-1.0.11.tar.bz2 ) && \
                       echo "d26c13eac99ac4504c532e8e76a1c8e4bd526471eb8a0a4ff2a88db60cb0b088  libXv-1.0.11.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXv-1.0.11.tar.bz2 && \
    cd /tmp/scratch/libXv-1.0.11 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXvMC-1.0.12.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXvMC-1.0.12.tar.bz2 ) && \
                       echo "6b3da7977b3f7eaf4f0ac6470ab1e562298d82c4e79077765787963ab7966dcd  libXvMC-1.0.12.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXvMC-1.0.12.tar.bz2 && \
    cd /tmp/scratch/libXvMC-1.0.12 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXxf86dga-1.1.5.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXxf86dga-1.1.5.tar.bz2 ) && \
                       echo "2b98bc5f506c6140d4eddd3990842d30f5dae733b64f198a504f07461bdb7203  libXxf86dga-1.1.5.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXxf86dga-1.1.5.tar.bz2 && \
    cd /tmp/scratch/libXxf86dga-1.1.5 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libXxf86vm-1.1.4.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libXxf86vm-1.1.4.tar.bz2 ) && \
                       echo "afee27f93c5f31c0ad582852c0fb36d50e4de7cd585fcf655e278a633d85cd57  libXxf86vm-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libXxf86vm-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libXxf86vm-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libdmx-1.1.4.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libdmx-1.1.4.tar.bz2 ) && \
                       echo "253f90005d134fa7a209fbcbc5a3024335367c930adf0f3203e754cf32747243  libdmx-1.1.4.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libdmx-1.1.4.tar.bz2 && \
    cd /tmp/scratch/libdmx-1.1.4 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libpciaccess-0.16.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libpciaccess-0.16.tar.bz2 ) && \
                       echo "214c9d0d884fdd7375ec8da8dcb91a8d3169f263294c9a90c575bf1938b9f489  libpciaccess-0.16.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libpciaccess-0.16.tar.bz2 && \
    cd /tmp/scratch/libpciaccess-0.16 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libxkbfile-1.1.0.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libxkbfile-1.1.0.tar.bz2 ) && \
                       echo "758dbdaa20add2db4902df0b1b7c936564b7376c02a0acd1f2a331bd334b38c7  libxkbfile-1.1.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxkbfile-1.1.0.tar.bz2 && \
    cd /tmp/scratch/libxkbfile-1.1.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libxshmfence-1.3.tar.bz2 || \
                         wget -t 3 https://www.x.org/pub/individual/lib/libxshmfence-1.3.tar.bz2 ) && \
                       echo "b884300d26a14961a076fbebc762a39831cb75f92bed5ccf9836345b459220c7  libxshmfence-1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxshmfence-1.3.tar.bz2 && \
    cd /tmp/scratch/libxshmfence-1.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --sysconfdir=/tools \
                                      --localstatedir=/var \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/cups-2.3.3-source.tar.gz || \
                         wget -t 3 https://ftp.osuosl.org/pub/blfs/conglomeration/cups/cups-2.3.3-source.tar.gz ) && \
                       echo "261fd948bce8647b6d5cb2a1784f0c24cc52b5c4e827b71d726020bcc502f3ee  cups-2.3.3-source.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf cups-2.3.3-source.tar.gz && \
    cd /tmp/scratch/cups-2.3.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/alsa-lib-1.2.2.tar.bz2 || \
                         wget -t 3 https://www.alsa-project.org/files/pub/lib/alsa-lib-1.2.2.tar.bz2 ) && \
                       echo "d8e853d8805574777bbe40937812ad1419c9ea7210e176f0def3e6ed255ab3ec  alsa-lib-1.2.2.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf alsa-lib-1.2.2.tar.bz2 && \
    cd /tmp/scratch/alsa-lib-1.2.2 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                      ./configure --prefix=/tools \
                                      --disable-static \
                                      && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-0.4.0.tar.bz2 || \
                         wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-0.4.0.tar.bz2 ) && \
                       echo "46e49469cb3b594af1d33176cd7565def2be3fa8be4371d62271fabb5eae50e9  xcb-util-0.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-0.4.0.tar.bz2 && \
    cd /tmp/scratch/xcb-util-0.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                           ./configure --prefix=/tools \
                                           --disable-static \
                                           && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-image-0.4.0.tar.bz2 || \
                         wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-image-0.4.0.tar.bz2 ) && \
                       echo "2db96a37d78831d643538dd1b595d7d712e04bdccf8896a5e18ce0f398ea2ffc  xcb-util-image-0.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-image-0.4.0.tar.bz2 && \
    cd /tmp/scratch/xcb-util-image-0.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                              ./configure --prefix=/tools \
                                              --disable-static \
                                            && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-keysyms-0.4.0.tar.bz2 || \
                         wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-keysyms-0.4.0.tar.bz2 ) && \
                       echo "0ef8490ff1dede52b7de533158547f8b454b241aa3e4dcca369507f66f216dd9  xcb-util-keysyms-0.4.0.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-keysyms-0.4.0.tar.bz2 && \
    cd /tmp/scratch/xcb-util-keysyms-0.4.0 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                                ./configure --prefix=/tools \
                                                --disable-static \
                                              && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-renderutil-0.3.9.tar.bz2 || \
                         wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-renderutil-0.3.9.tar.bz2 ) && \
                       echo "c6e97e48fb1286d6394dddb1c1732f00227c70bd1bedb7d1acabefdd340bea5b  xcb-util-renderutil-0.3.9.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-renderutil-0.3.9.tar.bz2 && \
    cd /tmp/scratch/xcb-util-renderutil-0.3.9 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                                   ./configure --prefix=/tools \
                                                   --disable-static \
                                                 && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-wm-0.4.1.tar.bz2 || \
                         wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-wm-0.4.1.tar.bz2 ) && \
                       echo "28bf8179640eaa89276d2b0f1ce4285103d136be6c98262b6151aaee1d3c2a3f  xcb-util-wm-0.4.1.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-wm-0.4.1.tar.bz2 && \
    cd /tmp/scratch/xcb-util-wm-0.4.1 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                           ./configure --prefix=/tools \
                                           --disable-static \
                                           && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/xcb-util-cursor-0.1.3.tar.bz2 || \
                         wget -t 3 https://xcb.freedesktop.org/dist/xcb-util-cursor-0.1.3.tar.bz2 ) && \
                       echo "05a10a0706a1a789a078be297b5fb663f66a71fb7f7f1b99658264c35926394f  xcb-util-cursor-0.1.3.tar.bz2" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf xcb-util-cursor-0.1.3.tar.bz2 && \
    cd /tmp/scratch/xcb-util-cursor-0.1.3 && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                                               ./configure --prefix=/tools \
                                               --disable-static \
                                             && \
                                    make -j$(nproc) && make -j$(nproc) install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libxkbcommon-0.10.0.tar.xz || \
                         wget -t 3 https://xkbcommon.org/download/libxkbcommon-0.10.0.tar.xz ) && \
                       echo "57c3630cdc38fb4734cd57fa349e92244f5ae3862813e533cedbd86721a0b6f2  libxkbcommon-0.10.0.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libxkbcommon-0.10.0.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               meson --prefix=/tools -Denable-docs=false -Denable-wayland=false ../libxkbcommon-0.10.0 && \
                             ninja && ninja install && \
    cd / && rm -rf /tmp/scratch && \
    mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/libdrm-2.4.102.tar.xz || \
                         wget -t 3 https://dri.freedesktop.org/libdrm/libdrm-2.4.102.tar.xz ) && \
                       echo "8bcbf9336c28e393d76c1f16d7e79e394a7fce8a2e929d52d3ad7ad8525ba05b  libdrm-2.4.102.tar.xz" | sha256sum -c && \
    cd /tmp/scratch && tar -xvf libdrm-2.4.102.tar.xz && \
    cd /tmp/scratch/build && CFLAGS="-O3 -march=${IMAGE_ARCH}" CXXFLAGS="-O3 -march=${IMAGE_ARCH}" \
                               meson --prefix=/tools ../libdrm-2.4.102 && \
                             ninja && ninja install && \
    cd / && rm -rf /tmp/scratch

ENV PATH /tools/java/openjdk/bin/:/tools/bin/:${PATH}
ENV PKG_CONFIG_PATH /tools/pkgconfig/:/tools/lib64/:/tools/lib64/pkgconfig/:/tools/lib/:/tools/lib/pkgconfig/
ENV LD_LIBRARY_PATH ""
ENV C_INCLUDE_PATH /tools/include/
ENV CPLUS_INCLUDE_PATH /tools/include/

COPY --from=bootstrap_java /lib/x86_64-linux-gnu /lib/x86_64-linux-gnu
COPY --from=bootstrap_java /tools/java/openjdk /tools/java/openjdk

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && git clone https://github.com/AdoptOpenJDK/openjdk-jdk8u.git && \
    cd /tmp/scratch/openjdk-jdk8u/ && git checkout da50732b9786710698109b8e3d50c0fe547952da && \
    unset LD_LIBRARY_PATH && \
    unset C_INCLUDE_PATH && \
    unset CPLUS_INCLUDE_PATH && \
    cd /tmp/scratch/openjdk-jdk8u/ && chmod +x ./configure && ./configure \
                                                                --prefix=/tools \
                                                                --with-extra-cflags="-O3 -march=${IMAGE_ARCH} -Wno-error -fcommon -fno-lifetime-dse -fno-delete-null-pointer-checks -I -std=c++03" \
                                                                --with-extra-cxxflags="-O3 -march=${IMAGE_ARCH} -Wno-error -fpermissive -I -std=c++03" && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|(-1) << FIRST_TYPE|(-(uint)(1)) << FIRST_TYPE|g' hotspot/src/share/vm/code/dependencies.hpp && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|GSS_FUNCTION_TABLE_PTR ftab;|extern GSS_FUNCTION_TABLE_PTR ftab;|g' jdk/src/solaris/native/sun/security/jgss/wrapper/NativeFunc.h && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|#include "NativeFunc.h"|#include "NativeFunc.h"\nGSS_FUNCTION_TABLE_PTR ftab;|g' jdk/src/solaris/native/sun/security/jgss/wrapper/NativeFunc.c && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|#include <sys/sysctl.h>||g' jdk/src/solaris/native/java/net/PlainDatagramSocketImpl.c && \
    cd /tmp/scratch/openjdk-jdk8u/ && sed -i 's|#include <sys/sysctl.h>||g' jdk/src/solaris/native/java/net/PlainSocketImpl.c && \
    cd /tmp/scratch/openjdk-jdk8u/ && make images && \
    cd /tmp/scratch/openjdk-jdk8u/ && mkdir -p /tools/ && cp -r build/*/images/j2sdk-image /tools/java && \
    cd / && rm -rf /tmp/scratch && \
    rm -rf /tools/java/openjdk

FROM tools AS build_rust

ARG IMAGE_ARCH
ARG LOCAL_SOURCES_SERVER

ENV PATH /tools/bin/:${PATH}
ENV PKG_CONFIG_PATH /tools/pkgconfig/:/tools/lib64/:/tools/lib64/pkgconfig/:/tools/lib/:/tools/lib/pkgconfig/:/tools/share/pkgconfig/
ENV LD_LIBRARY_PATH /tools/lib64/:/tools/lib/:${LD_LIBRARY_PATH}

RUN mkdir -p /tmp/scratch/build && \
    cd /tmp/scratch && ( wget -t 1 ${LOCAL_SOURCES_SERVER}/rust-1.47.0-x86_64-unknown-linux-gnu.tar.gz || \
                         wget -t 3 https://static.rust-lang.org/dist/rust-1.47.0-x86_64-unknown-linux-gnu.tar.gz ) && \
                       echo "d0e11e1756a072e8e246b05d54593402813d047d12e44df281fbabda91035d96  rust-1.47.0-x86_64-unknown-linux-gnu.tar.gz" | sha256sum -c && \
    cd /tmp/scratch && tar -xf rust-1.47.0-x86_64-unknown-linux-gnu.tar.gz && \
    cd /tmp/scratch && rust-1.47.0-x86_64-unknown-linux-gnu/install.sh --prefix=/tools
    
FROM scratch

COPY --from=tools /bin/sh /bin/sh
COPY --from=tools /bin/bash /bin/bash
COPY --from=tools /tools /tools
COPY --from=tools /etc/ /etc/
COPY --from=tools /usr/share/zoneinfo /usr/share/zoneinfo
COPY --from=build_openjdk /tools/java/ /tools/java/
COPY --from=build_rust /opt /opt

ENV PATH /tools/java/bin/:/tools/bin/:/tools/sbin/:${PATH}
ENV LD_LIBRARY_PATH /tools/lib/
ENV SSL_CERT_DIR /tools/ssl/certs/
ENV SSL_CERT_FILE /tools/ssl/certs/ca-certificates.crt
ENV JAVA_HOME /tools/java/

RUN mv /tools/java/j2sdk-image /tools/ && \
    rm -rf /tools/java && \
    mv /tools/j2sdk-image /tools/java

RUN mkdir -p /usr/ && ln -s /tools/bin /usr/ && \
    mkdir -p /lib64/ && ln -s /tools/lib/ld-linux-x86-64.so.2 /lib64/ld-linux-x86-64.so.2

RUN mkdir /tmp && chmod a+rwx /tmp

