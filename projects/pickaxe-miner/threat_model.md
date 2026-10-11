# Pickaxe Miner — threat model

## System and trust boundaries
Pickaxe Miner is an open-source Rust GPU miner for PHOTON, a mineable CashToken on Bitcoin Cash. It obtains covenant/baton state through Fulcrum/Electrum and validates chain state and broadcasts raw transactions via node RPC. It accepts untrusted network responses, configuration, user-provided payout addresses and potentially GPU/backend inputs. A locally configured payout address and the disclosed mining-work donation policy are intended behavior.

## High-priority security properties
- Reject forged, stale, malformed, or inconsistent transaction, target, baton, NFT or difficulty data before submitting a claim.
- Never redirect a winner's payout or change a user-selected payout address without explicit authorization; accurately account for the disclosed mining-work donation policy.
- Preserve PHOTON/CashToken contract validity and BCH transaction invariants; never confuse Chipnet with mainnet.
- Avoid network-triggered code execution, memory corruption, command injection, supply-chain execution and unsafe handling of GPU driver/FFI/kernel boundaries.
- Ensure unexpected GPU or provider failures do not silently manufacture rewards, expose secrets, or compromise the host.

## How to exercise
Run `cargo test --locked -- --skip if_cuda --skip if_wgpu --test-threads=1` for CPU-safe regression tests. The project includes Rust sources, test fixtures, native backend code and portable GPU components. The scanner VM may have no physical GPU; that is not itself a vulnerability.

## Reporting and severity
For every claim provide attacker control, reachable entry point, exact code path, prerequisites, affected network, reproducible test or precise exploit trace and expected versus observed behavior. Separate demonstrated bugs from hypotheses. Critical requires substantiated remote code execution, silent payout theft/redirection or similarly severe remote compromise. Do not rate a local crash, a performance issue, a GPU-less build, intentional fee/donation behavior or a hypothetical path as critical without a demonstrated security impact. Validate patches against tests and preserve protocol consensus rules.

## Do not do
No real wallet secrets, no destructive mainnet transactions, no unauthorized GPU workload and no requests for private credentials. Prefer offline fixtures and Chipnet-based examples where applicable.
