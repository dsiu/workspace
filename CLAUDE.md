# AGENTS.md

This file provides guidance to Claude Code (claude.ai/code) / Gemini (google) / Codex (openai) when working with code in this repository.

## Repository Overview

This is a **ReScript monorepo** managed with Yarn workspaces containing functional programming libraries and projects. The codebase uses **ReScript v12.0.0-rc.2** and targets ES modules with `.res.mjs` output files.

### Workspace Structure

- `packages/`: Reusable ReScript libraries
  - `rescript-stdlib-fp`: Custom FP standard library (based on Tablecloth, ported to use RescriptCore)
  - `rescript-relude`: Alternative prelude/standard library (port of Reazen/relude)
  - `rescript-bastet`: Type class library for FP
  - `rescript-relude-parse`: Parser combinators library
  - `rescript-bag`: OCaml backtracking/bag port
  - `rescript-graphology`: Graph data structures
  - `rescript-seq`: Sequence utilities
  - `rescript-extras`: Additional utilities
- `projects/`: Executable projects consuming the packages
  - `advent-of-code`: Advent of Code solutions
  - `rescript-snippets`: Code examples and snippets

### Key Architectural Patterns

**Workspace Dependencies**: Projects use `workspace:^` protocol to depend on local packages. The root `rescript.json` pins dependencies on projects, while individual packages can be developed independently.

**Module Output**: All packages compile to ES modules (`.res.mjs`) with `in-source: true`, meaning compiled JavaScript lives alongside source files.

**Testing Strategy**: Jest with `@swc/jest` transformer. Test files use `_Test.res` suffix (e.g., `Array_Test.res`) and compile to `.res.mjs` for execution.

**FP Library Layering**:
- Base layer: `rescript-bastet` provides type classes (Functor, Monad, etc.)
- Mid layer: `rescript-relude` builds on bastet with practical FP utilities
- Top layer: `rescript-stdlib-fp` provides Belt-free standard library using RescriptCore
- Specialized: `rescript-bag` for backtracking, `rescript-graphology` for graphs

## Build & Development Commands

### Root Level (all packages/projects)
```bash
yarn build          # Build all workspaces
yarn watch          # Watch mode with wireit
yarn clean          # Clean all build artifacts
yarn test           # Run all tests (stdlib-fp, advent-of-code, rescript-snippets)
```

### Individual Package/Project
```bash
yarn workspace <name> build       # e.g., yarn workspace @dsiu/rescript-stdlib-fp build
yarn workspace <name> test        # Run package tests
yarn workspace <name> watch       # Watch mode for specific package
```

### Specific Test Commands
```bash
# Root shortcuts
yarn test-rescript-stdlib-fp
yarn test-advent-of-code
yarn test-rescript-snippets

# Run specific test file
yarn workspace <name> jest <path>/_Test.res.mjs

# Examples:
yarn workspace @dsiu/rescript-stdlib-fp jest __tests__/List_Test.res.mjs
yarn workspace advent-of-code jest __tests__/2024/Day1_Test.res.mjs
```

### Advent of Code Specific
```bash
cd projects/advent-of-code
yarn reformat    # Run code formatter (uses scripts/reformat.sh)
```

## ReScript Configuration

**Common rescript.json patterns:**
- `"namespace": true` - Used in advent-of-code to avoid naming conflicts
- `"suffix": ".res.mjs"` - ES module output with .mjs extension
- `"package-specs": { "module": "esmodule", "in-source": true }`
- `"warnings": { "number": "-44-3" }` - Suppress unused opens and deprecated warnings
- `"warnings": { "error": "+101" }` - advent-of-code treats unused variables as errors

**Source directories:**
- Standard packages: `src/` and `__tests__/` with `subdirs: true`
- advent-of-code also includes: `interop/` for JS bindings

## Code Style & Conventions

### Programming Language
- use `ReScript v12`
- Please refer to https://rescript-lang.org/llms/manual/v12.0.0/llm-full.txt for ReScript language model guidelines and references.

### Naming
- Functions/variables: `camelCase`
- Modules/types/constructors: `PascalCase`
- Test files: `<Module>_Test.res` (e.g., `Array_Test.res`)
- Module prefixes: Libraries use namespace prefixes (e.g., `Stdlib__Array.res`, `Relude_Array.res`)

### Functional Programming Patterns
- Use `Result` or `Option` types for error handling, avoid exceptions
- Pattern matching preferred over if/else
- Explicit type annotations for function parameters and returns
- Immutable data structures by default
- Place `.resi` interface files beside `.res` implementations when needed

### Testing Patterns
- Use `open Jest` and `open Expect` in test files
- Test files go in `__tests__/` directory with `_Test.res` suffix
- Jest config matches `**/*_Test.res.mjs` pattern
- Transform ignores: Various workspace packages added to `transformIgnorePatterns`

## Package Manager

Uses **Yarn v4.10.3** (Berry) with workspaces. Never use npm commands - always use `yarn`.

## Dependencies & Interop

**Test Framework**: `@glennsl/rescript-jest` (forked version `github:dsiu/rescript-jest#rescript-v12` for v12 compatibility)

**Common Dependencies Across Projects:**
- Most packages depend on `rescript ^12.0.0-rc.2`
- advent-of-code uses: `rescript-nodejs`, `rescript-schema`, `debug`, `ms`
- Multiple packages use custom workspace dependencies via `workspace:^`

**Transform Patterns**: Jest configs must include all workspace packages in `transformIgnorePatterns` to allow Jest to process them.

## Important Notes

- This is a **bleeding-edge** codebase using ReScript v12 RC versions
- Some dependencies use custom forks (see `github:dsiu/*` references in package.json files)
- The codebase is transitioning away from Belt to RescriptCore (see rescript-stdlib-fp)
- Advent of Code project organizes solutions by year/day (e.g., `src/2024/Day1/`)
