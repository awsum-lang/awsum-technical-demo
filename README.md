# awsum-technical-demo

Reproducible demo for the [Awsum](https://awsum-lang.org) compiler. Runs identically on all five targets — LLVM, JVM, CLR, WASM, JS — against a released `awsum` binary.

## 1. Install

Install `awsum`, the target runtimes you want to exercise, and (optionally) an editor extension via [awsum-lang.org/install](https://awsum-lang.org/install).

## 2. Get the demo

```sh
git clone https://github.com/awsum-lang/awsum-technical-demo
cd awsum-technical-demo
```

## 3. Run

### `recursion-and-memory-safety/`

- Builds an immutable tree of depth 100,000
- mirrors it 500 times (multi-child non-tail recursion and heavy allocation pressure)
- then walks the deepest left path (three-function mutual tail recursion)

```sh
awsum run --program-type cli -t llvm recursion-and-memory-safety/Main.aww
awsum run --program-type cli -t jvm  recursion-and-memory-safety/Main.aww
awsum run --program-type cli -t clr  recursion-and-memory-safety/Main.aww
awsum run --program-type cli -t wasm recursion-and-memory-safety/Main.aww
awsum run --program-type cli -t js   recursion-and-memory-safety/Main.aww
```

Allow each command a few seconds to finish. Expected stdout on every target: `100000` (no trailing newline).

What it shows:

- **Stack-safe recursion of any shape, without user-facing annotations.** Self, non-tail (the `mirror` body has two recursive calls), and three-function mutual (`deepestLeftA`/`B`/`C`) all run in bounded stack on every target — including JVM and JS, where no native cross-method TCO exists. No `tailRecM`, no manual CPS, no `@tailrec`.
- **Memory safety without a GC opt-in.** LLVM and WASM use compiler-emitted reference counting (on macOS, `leaks --atExit -- awsum run --program-type cli -t llvm recursion-and-memory-safety/Main.aww` verifies zero leaks under the `mirrorN 500` allocation pressure). JVM, CLR, and JS use the host GC. The `.aww` source has no lifetime annotations either way.

## AI use

This demo is developed with substantial usage of generative AI. Every generated change is reviewed, edited, and accepted by a human before it lands in the repository, and no output is shipped unedited.

## License

Apache 2.0 — see [LICENSE](LICENSE) and [NOTICE](NOTICE).
