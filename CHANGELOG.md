# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

`awsum-technical-demo` is tagged in lockstep with the `awsum` compiler — the tag `vA.B.C` bookmarks the commit at which every demo in this repo was verified to run cleanly under `awsum A.B.C`. The compiler version this repo targets lives in [`AWSUM_VERSION`](.github/workflows/ci.yml). Only the latest tag is supported.

Until `awsum 1.0.0`, the project does not follow SemVer — every release increments only the patch (`0.0.1 → 0.0.2 …`), and any release may break. The lockstep above is the contract that does hold: within a single `0.0.x`, every demo in this repo runs under `awsum 0.0.x` and produces the expected stdout on every supported target.

## [Unreleased]

### Added

- Initial release. `recursion-and-memory-safety/` demo — builds an immutable tree of depth 100,000, mirrors it 500 times (multi-child non-tail recursion and heavy allocation pressure), walks the deepest left path via three-function mutual tail recursion. CI runs the demo through every target (`llvm`, `jvm`, `clr`, `wasm`, `js`) on every supported host OS and asserts the expected stdout.
- `just format` / `just test` / `just fix` / `just release` recipes for local development, mirroring the other `awsum-lang/*` repos.

### Changed

- CI runs the demo on the runtimes `awsum` 0.0.8 targets: Java 25, .NET 10, Node.js 26 (previously Java 11, .NET 9, Node.js 22). `actions/setup-java` and `actions/setup-dotnet` v5 → v6, `actions/setup-node` v6 → v7. Every GitHub Action is pinned to the commit SHA of a release at least 7 days old, and the runtimes to exact versions (Temurin 25.0.5, .NET SDK 10.0.403, Node.js 26.13.0, wasmtime 50.0.1).
