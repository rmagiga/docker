FROM ubuntu:20.10

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    curl \
    nasm \
    build-essential \
    bison \
    flex \
    libgmp3-dev \
    libmpc-dev \
    libmpfr-dev \
    texinfo \
&& apt-get clean \
&& rm -rf /var/lib/apt/lists/*

ARG SRCDIR=/tmp/src
ARG TARGET=i686-elf
ARG BINUTILS_VERSION="2.30"
ARG GCC_VERSION="8.1.0"

ENV PREFIX="/usr/local/cross"
ENV PATH="$PREFIX/bin:$PATH"

WORKDIR ${SRCDIR}

RUN curl -sO https://ftp.gnu.org/gnu/binutils/binutils-${BINUTILS_VERSION}.tar.gz \
  && tar -xf binutils-${BINUTILS_VERSION}.tar.gz \
  && rm binutils-${BINUTILS_VERSION}.tar.gz
RUN curl -sO https://ftp.gnu.org/gnu/gcc/gcc-${GCC_VERSION}/gcc-${GCC_VERSION}.tar.gz \
  && tar -xf gcc-${GCC_VERSION}.tar.gz \
  && rm gcc-${GCC_VERSION}.tar.gz

WORKDIR ${SRCDIR}/build-binutils
RUN ../binutils-${BINUTILS_VERSION}/configure --target=${TARGET} --prefix="${PREFIX}" \
  --with-sysroot --disable-nls --disable-werror \
  && make \
  && make install

WORKDIR ${SRCDIR}/build-gcc
RUN ../gcc-${GCC_VERSION}/configure --target=$TARGET --prefix="$PREFIX" --disable-nls \
  --enable-languages=c,c++ --without-headers \
  && make -j$((`nproc`+1)) all-gcc \
  && make -j$((`nproc`+1)) all-target-libgcc \
  && make install-gcc \
  && make install-target-libgcc

RUN which -- $TARGET-as || echo $TARGET-as is not in the PATH

RUN $TARGET-gcc --version
