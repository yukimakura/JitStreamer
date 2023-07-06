ARG IMAGE=arm64v8/ubuntu:21.10

FROM $IMAGE

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=America/New_York

RUN apt update
RUN apt install -y git pkg-config autoconf automake libtool make build-essential python3 libssl-dev

RUN mkdir /buildenv

# Build the dependencies

WORKDIR /buildenv

RUN git clone https://github.com/libimobiledevice/libplist.git; 
WORKDIR /buildenv/libplist
RUN ./autogen.sh --enable-static --disable-shared --without-cython; make -j`$nproc`; make install;

WORKDIR /buildenv

RUN git clone https://github.com/libimobiledevice/libimobiledevice-glue.git; 
WORKDIR /buildenv/libimobiledevice-glue
RUN ./autogen.sh --enable-static --disable-shared --without-cython; make -j`$nproc`; make install;

WORKDIR /buildenv

RUN git clone https://github.com/libimobiledevice/libusbmuxd.git; 
WORKDIR /buildenv/libusbmuxd
RUN ./autogen.sh --enable-static --disable-shared --without-cython; make -j`$nproc`; make install;

WORKDIR /buildenv

RUN git clone https://github.com/libimobiledevice/libimobiledevice.git; 
WORKDIR /buildenv/libimobiledevice
RUN ./autogen.sh --enable-static --disable-shared --without-cython; make -j`$nproc`; make install;

WORKDIR /buildenv

RUN apt remove -y libssl-dev
RUN apt install -y libssl-dev
# RUN git clone https://github.com/openssl/openssl.git --verbose --progress;
# WORKDIR /buildenv/openssl
# RUN ./Configure -static --static -mfpu=neon; make -j`$nproc`; make install;

# Rust install
RUN apt install -y curl
ENV RUST_HOME /usr/local/lib/rust
ENV RUSTUP_HOME ${RUST_HOME}/rustup
ENV CARGO_HOME ${RUST_HOME}/cargo
RUN mkdir /usr/local/lib/rust && \
    chmod 0755 $RUST_HOME
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs > ${RUST_HOME}/rustup.sh \
    && chmod +x ${RUST_HOME}/rustup.sh \
    && ${RUST_HOME}/rustup.sh -y --default-toolchain nightly --no-modify-path
ENV PATH $PATH:$CARGO_HOME/bin

WORKDIR /buildenv

# JitStreamer build time
RUN git clone https://github.com/jkcoxson/plist_plus.git;
RUN git clone https://github.com/jkcoxson/rusty_libimobiledevice.git;
RUN git clone https://github.com/jkcoxson/JitStreamer.git;

WORKDIR /buildenv/JitStreamer
RUN git reset --hard 59aa79fdf16a45d254a88d7257491f22005c8eb2
RUN RUSTFLAGS="-C target-feature=+crt-static" cargo build --target x86_64-unknown-linux-gnu --release --verbose
