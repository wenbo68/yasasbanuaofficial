# Dependency Overview

- Date: 2026/06/07
- Sources: none found

## Note

No dependency manifest of any kind is present in this repository. This repo is a source-only archival mirror of leaked Claude Code source code (recovered from an npm sourcemap), and it ships only the `src/` TypeScript/TSX tree plus README image assets — it does not include the package's build or dependency configuration.

The following manifest types were searched for across the entire repo (excluding `.git/`) and none were found:

- `package.json` (npm dependencies / devDependencies)
- `pyproject.toml` (`[project.dependencies]` / optional groups)
- `requirements*.txt` (pip)
- `go.mod` (Go modules)
- `Cargo.toml` (Rust crates)
- `Gemfile` (Ruby gems)
- `pom.xml` / `build.gradle` (Java/Maven/Gradle)
- lockfiles (`*.lock`)

Because there is no manifest, no dependency List or Taxonomy can be produced. The README states the project is built with Bun/Node and TypeScript and relies on libraries such as Commander.js (CLI) and a custom fork of Ink (React-based terminal rendering), but these are not declared in any tracked manifest within this repository and therefore cannot be enumerated authoritatively here.
