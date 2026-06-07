# Dependency Overview

No `package.json`, `bun.lockb`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pyproject.toml`, or other dependency manifest file is present in this repository.

## Why

This repo is a **source code archive / research mirror** of Anthropic's Claude Code CLI, reconstructed from a leaked npm sourcemap. It does not include the original build configuration or lock files — only the raw TypeScript/TSX source files and documentation assets.

## Known Dependencies (from source analysis)

Although no manifest is committed, the source files reveal the following key dependencies used by the original project:

| Dependency | Role |
|------------|------|
| **Bun** (runtime) | JavaScript runtime and bundler replacing Node.js |
| **React** + **react-jsx** | UI component model (used with Ink for terminal rendering) |
| **Ink** (forked, `@ant/ink`) | Terminal UI framework (React renderer for the terminal) |
| **Commander.js** | CLI argument parsing (`src/main.tsx`) |
| **@anthropic-ai/sdk** | Anthropic API client for streaming LLM calls |
| **Zod** | Runtime schema validation (referenced in `src/schemas/`) |
| **TypeScript** | Language (strict mode enforced) |

To install and run the original project you would need its `package.json` and lock file, which are not part of this archive.
