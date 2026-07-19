# ZeroClaw Solana Plugins

A collection of T0 (read-only) Solana-native plugins for the [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw) AI agent runtime, written in Rust and compiled to `wasm32-wasip2` WIT components.

## Plugins

| Plugin | Track | Tier | Description |
|---|---|---|---|
| [`token-risk-check`](plugins/token-risk-check/) | D: Intelligence | T0 | Token-2022 risk assessment — mint authority, holder concentration, transfer hooks |
| [`wallet-narrate`](plugins/wallet-narrate/) | D: Intelligence | T0 | Transaction → human-readable narrative |
| [`sns-resolve`](plugins/sns-resolve/) | D: Intelligence | T0 | .sol / ANS domain resolution |
| [`portfolio-brief`](plugins/portfolio-brief/) | B: DeFi | T0 | Wallet portfolio summary shaped to ~200 tokens |
| [`lending-health`](plugins/lending-health/) | B: DeFi | T0 | Lending position health factor for Kamino/MarginFi/Drift |

## Bounty

This collection is submitted for the **ZeroClaw Solana Bounty** on [Superteam Earn](https://superteam.fun/earn/listing/zeroclaw).

**Total prize pool:** $5,000 USDG

- 🥇 1st: $1,800
- 🥈 2nd: $1,200
- 🥉 3rd: $1,000
- Honorable mentions (×4): $250 each

**Deadline:** August 7, 2026

## Structure

Each plugin follows the **pure-core / thin-shim** pattern:

```
plugins/<name>/
├── Cargo.toml          # crate-type = ["cdylib", "rlib"]
├── manifest.toml       # ZeroClaw plugin manifest
├── README.md           # Full docs + threat model + prompt-injection test
├── src/
│   ├── lib.rs          # wasm component shim (#[cfg(target_family = "wasm")])
│   └── <plugin>.rs     # Pure logic, no wasm deps — `cargo test` works host-side
└── tests/
    └── core.rs         # Host-run integration tests (no wasm toolchain)
```

## Build all

```bash
rustup target add wasm32-wasip2

for plugin in plugins/*/; do
    (cd "$plugin" && cargo build --target wasm32-wasip2 --release)
done
```

## Test all

```bash
for plugin in plugins/*/; do
    (cd "$plugin" && cargo test --lib)
done
```

## Submission

- PR: [zeroclaw-labs/zeroclaw-plugins](https://github.com/zeroclaw-labs/zeroclaw-plugins)
- Demo video: ≤ 3 minutes (Telegram + terminal)
- One-pager: see each plugin's README

## Design notes

- All 5 plugins are **T0 (read-only)** — sweet spot for safety + utility
- Pure Rust cores tested with `cargo test --lib` (no wasm toolchain needed)
- No `solana-sdk` or `solana-client` — assembled via `serde_json` + raw RPC calls
- Output shaped to ~200 tokens to avoid context window flooding
- Every plugin includes a prompt-injection test demonstrating fail-closed behavior

## References

- [ZeroClaw repo](https://github.com/zeroclaw-labs/zeroclaw) · [docs](https://docs.zeroclawlabs.ai)
- [WIT contract](https://github.com/zeroclaw-labs/zeroclaw-plugins/tree/main/wit/v0)
- [Plugin authoring guide](https://github.com/zeroclaw-labs/zeroclaw/tree/master/docs/book/src/plugins)
- [Reference plugin](https://github.com/zeroclaw-labs/zeroclaw-plugins/tree/main/plugins/redact-text)
