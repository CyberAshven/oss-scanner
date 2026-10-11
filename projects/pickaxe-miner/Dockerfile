FROM rust:bookworm

ENV DEBIAN_FRONTEND=noninteractive \
    CARGO_TERM_COLOR=never \
    RUSTFLAGS="-C debuginfo=2 -C force-frame-pointers=yes"

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates git pkg-config clang cmake make g++ python3 \
    libssl-dev libudev-dev libvulkan-dev libx11-dev libxkbcommon-dev \
    && rm -rf /var/lib/apt/lists/*

COPY . /src
WORKDIR /src

# Preserve the full, default-feature build for static code review.
RUN cargo fetch --locked
RUN cargo build --locked --lib --bins

# Compile test binaries without requiring hardware or live network access.
RUN cargo test --locked --no-run
