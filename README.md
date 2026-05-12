# Vacietis

Vacietis is a C compiler implemented in Common Lisp.  
It reads C source into Lisp forms, then runs that code inside the Lisp runtime.

## Highlights

- C code is read/compiled as Lisp forms with Vacietis readtables.
- Includes a portable Lisp-based libc implementation.
- Ships test coverage for reader, runtime semantics, and end-to-end C programs.
- Works well with **SBCL on Windows 11**.

## Repository layout

- `compiler/` — C reader/parser, type model, and operator/runtime semantics.
- `libc/` — libc headers/functions implemented in Lisp.
- `runtime/` — C program execution entry (`run-c-program`).
- `test/` — reader tests, basic runtime tests, and fixture C programs.
- `vcc/` — toy compiler executable builder (`vacietis.vcc` system).

## Quick start (SBCL on Windows 11)

### 1. Prerequisites

1. Install **SBCL**.
2. Install **Quicklisp**.
3. Clone this repository.

### 2. Make the system visible to Quicklisp

Put this repository in Quicklisp local projects (recommended), e.g.:

`%USERPROFILE%\quicklisp\local-projects\Vacietis`

### 3. Load Vacietis

```lisp
(ql:quickload "vacietis")
```

If the project is not in Quicklisp local-projects, you can load local ASDF files directly:

```lisp
(require :asdf)
(asdf:load-asd (truename "vacietis.asd"))
(asdf:load-system :vacietis)
```

## Usage

Read C forms from a stream:

```lisp
(let ((*readtable*               vacietis:c-readtable)
      (vacietis:*compiler-state* (vacietis:make-compiler-state)))
  (read some-stream))
```

Load a C file:

```lisp
(vacietis:load-c-file "C:/path/to/file.c")
```

Run the loaded program package:

```lisp
(vacietis:run-c-program some-package)
```

## Build `vcc` executable

```lisp
(ql:quickload "vacietis.vcc")
```

This builds `vcc/vcc` in the repository.  
`vcc` can compile a single C file into an executable image (`a.out`).

## Testing

Load and run all tests:

```lisp
(ql:quickload "vacietis.test")
(vacietis.test:run-tests)
```

Run a single test:

```lisp
(eos:run! 'vacietis.test.reader::preprocessor-nested)
```

## Recent updates

- Improved reader behavior on SBCL/Windows newline handling.
- Fixed preprocessor define evaluation path in reader tests.
- Added resilient `scanf`/`sscanf`/`fscanf` fallback wiring in libc when `scanf.c` is not available through the C reader.
- Added `.github/copilot-instructions.md` for better AI-assisted contribution workflow.

## Technical notes

- Vacietis uses the Lisp memory model directly; primitive C `sizeof` values are effectively `1`.
- Pointer/lvalue behavior is modeled via Lisp-level pointer abstractions.
- Some C code that assumes byte-level type-punning over `char[]` may not behave as in native C runtimes.

## License

Vacietis is licensed under **LLGPL v3 or later**.  
See `LICENSE` for details.

Portions of libc may derive from Zeta-C (public domain) and uClibc (LGPL).
