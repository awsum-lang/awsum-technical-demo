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

What it demonstrates:

- **Stack-safe recursion of any shape, without user-facing annotations.** Self, non-tail (the `mirror` body has two recursive calls), and three-function mutual (`deepestLeftA`/`B`/`C`) all run in bounded stack on every target — including JVM and JS, where no native cross-method TCO exists. No `tailRecM`, no manual CPS, no `@tailrec`.
- **Memory safety without a GC opt-in.** LLVM and WASM use compiler-emitted reference counting (on macOS, `leaks --atExit -- awsum run --program-type cli -t llvm recursion-and-memory-safety/Main.aww` verifies zero leaks under the `mirrorN 500` allocation pressure). JVM, CLR, and JS use the host GC. The `.aww` source has no lifetime annotations either way.

See also:

- Human-readable snapshots of what this program compiles into — from the compiler's benchmark copy, which differs only in running a deeper tree with fewer mirrors — per target: [LLVM](https://github.com/awsum-lang/awsum/blob/v0.0.8/.snapshots/benchmark/recursion-and-memory-safety/compiler/compiled.posix.ll), [JVM](https://github.com/awsum-lang/awsum/blob/v0.0.8/.snapshots/benchmark/recursion-and-memory-safety/compiler/compiled.j), [CLR](https://github.com/awsum-lang/awsum/blob/v0.0.8/.snapshots/benchmark/recursion-and-memory-safety/compiler/compiled.il), [WASM](https://github.com/awsum-lang/awsum/blob/v0.0.8/.snapshots/benchmark/recursion-and-memory-safety/compiler/compiled.wat), [JS](https://github.com/awsum-lang/awsum/blob/v0.0.8/.snapshots/benchmark/recursion-and-memory-safety/compiler/compiled.js)
- [How safe is recursion in other languages](https://github.com/awsum-lang/language-comparison/tree/v0.0.8/stack-safe-recursion):
  - In Awsum and Haskell, recursion is stack-safe;
  - C#, Elm, F#, Go, Java, Koka, OCaml, PureScript, Roc, Rust, Scala, TypeScript, and Zig all overflow the stack and crash at runtime. Don't believe it? Every example is runnable, see for yourself.

## AI use

This demo is developed with substantial usage of generative AI.

## License

Apache 2.0 — see [LICENSE](LICENSE) and [NOTICE](NOTICE).
