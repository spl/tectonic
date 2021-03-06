# Copyright 2018 The Tectonic Project
# Licensed under the MIT License.

FROM alpine:edge

RUN apk update && \
    apk add \
        g++ \
        git \
        rust \
        cargo \
        fontconfig-dev \
        freetype-static \
        glib-static \
        graphite2-dev \
        graphite2-static \
        harfbuzz-dev \
        harfbuzz-static \
        icu-dev \
        icu-static \
        openssl-dev \
        zlib-dev

ADD pkg-config-rs.sh /
RUN /pkg-config-rs.sh

ENV PKG_CONFIG_ALL_STATIC=1
ENV OPENSSL_STATIC=1
ENV OPENSSL_DIR=/usr

# cc-rs does not support static linking stdc++,
# so we omit linking information on build.rs by setting CXXSTDLIB=''
# and specify static linking in RUSTFLAGS
ENV CXXSTDLIB=""
ENV RUSTFLAGS="-L /usr/lib -l static=stdc++ -C target-feature=+crt-static"

# Use a patched pkg-config-rs to allow static linking with system libraries.
# The --no-default-features flag removes serde-derive as a dep, which doesn't
# work when linking statically (rust-lang#40147).
CMD cd /tectonic && \
    echo -e "[patch.crates-io]\npkg-config = { path = \"/pkg-config-rs\" }" >> Cargo.toml && \
    cargo test --release --no-default-features
