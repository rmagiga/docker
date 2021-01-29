FROM alpine:3.13.0

RUN apk update
RUN apk add --no-cache --virtual download-dependencies \
  curl
RUN apk add --no-cache --virtual build-dependencies \
  build-base \
  flex \
  bison \
  gmp-dev \
  mpfr-dev \
  mpc1-dev \
  cloog-dev \
  file
RUN apk add --no-cache \
    gmp \
    mpc1 \
    cloog

ARG SRCDIR=/tmp/src
ARG TARGET=i686-elf
ARG BINUTILS_VERSION="2.35.1"
ARG GCC_VERSION="10.2.0"

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
