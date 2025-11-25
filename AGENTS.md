# AGENTS.md

This is a ReScript monorepo using Yarn v4 workspaces. Follow these guidelines when developing.

**Build/Test Commands**
- Build all: `yarn build`
- Watch mode: `yarn watch`
- Clean build: `yarn clean`
- Run all tests: `yarn test`
- Run project tests: `yarn test-advent-of-code`, `yarn test-rescript-snippets`
- Run single test file: `yarn workspace <pkg> jest <path>/<File>_Test.res.mjs`
- Build a single package: `yarn workspace <pkg> build`

**Code Style**
- ReScript v12, ES modules, in‑source `.res.mjs`
- camelCase for functions/vars; PascalCase for modules/types/constructors
- Prefer pattern matching over if/else
- Use `Option`/`Result`; avoid exceptions
- One `expect` per test; use tuple expects for multi-value assertions
- Keep imports minimal; avoid unused opens
- Immutable by default; avoid mutation
- Provide explicit types for public functions
- `.resi` interface files should mirror module boundaries

**Project Rules**
- Graphology APIs often return `Nullable`; convert via `Nullable.toOption`
- Respect Cursor/Copilot rules if present in `.cursor/` or `.github/copilot-instructions.md`
- Never use npm; use Yarn workspace commands only
- Use Conventional Commits for all commit messages
