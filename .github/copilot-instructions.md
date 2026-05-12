# Copilot instructions for Vacietis

## Build, test, and lint commands

This repository is organized as ASDF systems and is typically driven from a Common Lisp REPL with Quicklisp.

- Load compiler/runtime system:
  - `(ql:quickload "vacietis")`
- Build the toy compiler executable (`vcc/vcc`, via `trivial-dump-core`):
  - `(ql:quickload "vacietis.vcc")`
- Run full test suite:
  - `(ql:quickload "vacietis.test")`
  - `(vacietis.test:run-tests)`
- Run a single test case (Eos):
  - `(eos:run! 'vacietis.test.reader::preprocessor-nested)`

There is no dedicated lint/format command configured in this repository.

## High-level architecture

- `vacietis.asd` wires the system in layers: `compiler` -> `libc` -> `runtime`.
- `compiler/reader.lisp` is the core frontend: a combined tokenizer/preprocessor/parser that reads C syntax through `c-readtable` and emits Lisp forms.
  - `cstr` parses C from strings.
  - `load-c-file` parses/loads C files with a fresh compiler state.
  - Preprocessor state (`#define`, `#if`, `#include`, typedefs, structs, enums, var types) lives in `*compiler-state*`.
- `compiler/implementation.lisp` defines C operator/runtime semantics in Lisp (arithmetic, control flow macros, assignments, pointer-like behavior).
- `compiler/type.lisp` models C types (`pointer-to`, `struct-type`, `array-type`, etc.) and `type-size`; primitive types are represented with size `1` in this model.
- `libc/` is a Lisp-implemented libc surface:
  - header packages are defined in `libc/package.lisp` as `vacietis.libc.<header>.h`.
  - `include-libc-file` bridges `#include <...>` to these packages and optional companion source files under `libc/`.
- `runtime/program.lisp` executes compiled C entrypoints by calling `MAIN` in the target package and wiring stdio through `vacietis.libc.stdio.h::FILE`.
- `vacietis.vcc` (`vcc/vcc.lisp`) produces a standalone executable that loads one C file and dumps an `a.out` image that runs `main`.

## Key conventions in this codebase

- Most implementation files use `(in-readtable vacietis)`; do not assume standard CL reader behavior when editing parser/runtime files.
- C tokens/operators/keywords are represented in package `vacietis.c` (`vacietis.c:+`, `vacietis.c:if`, etc.). Keep this namespacing when adding parser or operator logic.
- Parser and preprocessor behavior is stateful through special variables (`*compiler-state*`, `*c-file*`, `*line-number*`, and local parser dynamic vars). New reader logic should preserve this dynamic-state pattern.
- `#include "..."` is resolved relative to the current source file; `#include <...>` uses `compiler-state-include-paths`, then falls back to builtin libc package loading.
- Tests are written with Eos macros defined in `test/test.lisp`:
  - `reader-test` for reader/form translation
  - `eval-test` for expression/runtime evaluation
  - `program-test` for fixture programs under `test/programs/<test-name>/main.c`
- Program tests isolate execution by creating a temporary package (`do-with-temp-c-package`) and loading fixture C files via `load-c-file`.
