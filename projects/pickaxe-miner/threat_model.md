# Pickaxe Miner — threat model

## System and trust boundaries
Pickaxe Miner is an open-source Rust GPU miner for PHOTON, a mineable CashToken on Bitcoin Cash. It obtains covenant/baton state through Fulcrum/Electrum and validates chain state and broadcasts raw transactions via node RPC. It accepts untrusted network responses, configuration, user-provided payout addresses, and GPU/backend inputs. A locally configured payout address and the disclosed mining-work donation policy are intended behavior.

## High-priority security properties
- Reject forged, stale, malformed, or inconsistent transaction, target, baton, NFT, or difficulty data before submitting a claim.
- Never redirect a winner's payout or change a user-selected payout address without explicit authorization; accurately account for the disclosed mining-work donation policy.
- Preserve PHOTON/CashToken contract validity and BCH transaction invariants; never confuse Chipnet with mainnet.
- Avoid network-triggered code execution, memory corruption, command injection, supply-chain execution, and unsafe handling of GPU driver/FFI/kernel boundaries.
- Ensure unexpected GPU or provider failures do not silently manufacture rewards, expose secrets, or compromise the host.

## How to exercise
The Dockerfile builds the native miner and executes CPU-safe tests in `/src`. Reproduce the project's Linux CI command with `cargo test --locked --all-features --no-fail-fast -- --skip if_cuda --skip if_wgpu`; independently test the shared Rust engine using `cargo test --locked --manifest-path rust-engine/Cargo.toml`. Other Rust unit tests and fixtures can be exercised offline. Some ignored tests and native GPU kernels need hardware or a dedicated software Vulkan setup; an ordinary scanner VM may not have GPUs. A missing GPU is not itself a vulnerability.

## Reporting and severity
For every claim provide attacker control, reachable entry point, exact code path, prerequisites, affected network, reproducible test or precise exploit trace, and expected versus observed behavior. Separate demonstrated bugs from hypotheses. Critical requires substantiated remote code execution, silent payout theft/redirection, or similarly severe remote compromise. Do not rate a local crash, a performance issue, a GPU-less build, intentional fee/donation behavior, or a hypothetical path as critical without a demonstrated security impact. Validate patches against tests and preserve protocol consensus rules.

## Do not do
No real wallet secrets, no destructive mainnet transactions, no unauthorized GPU workload, and no requests for private credentials. Prefer offline fixtures and Chipnet-based examples where applicable.
