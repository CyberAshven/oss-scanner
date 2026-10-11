FROM rust:bookworm

ENV DEBIAN_FRONTEND=noninteractive \
    CARGO_TERM_COLOR=never \
    RUSTFLAGS="-C debuginfo=2 -C force-frame-pointers=yes"

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates git pkg-config clang cmake make g++ python3 \
    libssl-dev libudev-dev libvulkan-dev libx11-dev libxkbcommon-dev \
    && rm -rf /var/lib/apt/lists/*

# OSS Scanner supplies the Pickaxe Miner checkout as the Docker build context.
COPY . /src
WORKDIR /src

# Fetch all dependencies while the build environment has Internet access.
RUN cargo fetch --locked

# Compile the full default mining application, including the portable GPU engine.
RUN cargo build --locked --lib --bins

# Run CPU-safe Rust tests using the same GPU exclusions as the project's Linux CI.
# Do not silence failures: an unhealthy scanner build should be reported.
RUN cargo test --locked --all-features --no-fail-fast -- --skip if_cuda --skip if_wgpu

# Test the shared Rust mining engine independently.
RUN cargo test --locked --manifest-path rust-engine/Cargo.toml
