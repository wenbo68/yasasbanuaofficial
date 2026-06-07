# Filesystem Overview

## Summary

This repository is an archival mirror of Anthropic's Claude Code CLI source code, which was inadvertently exposed via a sourcemap file bundled into the published npm package (discovered March 31, 2026 by @Fried_rice). The repo documents the leak, explains how it happened, and preserves the leaked TypeScript/TSX source for research and educational purposes.

Claude Code is a large terminal-based AI coding assistant built with:
- **Runtime**: Bun (not Node.js)
- **UI framework**: React/Ink (terminal renderer)
- **Entry point**: `src/entrypoints/cli.tsx` → `src/main.tsx` (785 KB)
- **Language**: TypeScript strict mode
- **Core LLM loop**: `src/query.ts` + `src/QueryEngine.ts`
- **Tool count**: 40+ agent tools under `src/tools/`

The README blog post is also published at https://kuber.studio/blog/AI/Claude-Code's-Entire-Source-Code-Got-Leaked-via-a-Sourcemap-in-npm,-Let's-Talk-About-it.

---

## Directory Tree

```
yasasbanuaofficial/
├── README.md                        # Blog-style writeup explaining the npm sourcemap leak and repo contents
│
├── .claude/                         # Claude Code agent/skill configuration (not expanded)
│   ├── agents/
│   ├── commands/
│   └── skills/
│
├── assets/                          # Images referenced in README
│   ├── claude-logo.png              # Claude logo
│   ├── claude-npm-img.png           # Screenshot of sourcemap exposure in npm
│   └── x-post.png                  # Screenshot of the original Twitter/X discovery post
│
└── src/                             # Full leaked Claude Code TypeScript source
    ├── main.tsx                     # Commander.js CLI definition (~785 KB, ~5600+ lines)
    ├── query.ts                     # Core API query function — streaming, tool calls, turn loop (~69 KB)
    ├── QueryEngine.ts               # Higher-level LLM orchestrator wrapping query() (~47 KB)
    ├── Tool.ts                      # Tool interface definition and utilities (~29 KB)
    ├── tools.ts                     # Tool registry — assembles the active tool list (~17 KB)
    ├── commands.ts                  # Slash-command implementations (~25 KB)
    ├── context.ts                   # System/user context builder (git status, CLAUDE.md, memory) (~6 KB)
    ├── setup.ts                     # One-time initialization — config, trust dialog (~21 KB)
    ├── history.ts                   # Conversation history persistence (~14 KB)
    ├── cost-tracker.ts              # Token cost tracking (~11 KB)
    ├── costHook.ts                  # Cost hook utilities (~617 B)
    ├── ink.ts                       # Ink render wrapper with ThemeProvider (~4 KB)
    ├── interactiveHelpers.tsx       # Interactive UI helpers (~57 KB)
    ├── dialogLaunchers.tsx          # Dialog/modal launchers (~22 KB)
    ├── replLauncher.tsx             # REPL session launcher (~4 KB)
    ├── Task.ts                      # Task type definitions (~3 KB)
    ├── tasks.ts                     # Task utilities (~1 KB)
    ├── projectOnboardingState.ts    # Project onboarding state management (~2 KB)
    │
    ├── entrypoints/                 # CLI & SDK entry points
    │   ├── cli.tsx                  # True entrypoint — fast paths (--version, MCP, daemon, etc.) (~39 KB)
    │   ├── init.ts                  # One-time initialization entrypoint (~14 KB)
    │   ├── mcp.ts                   # MCP server entrypoint (~6 KB)
    │   ├── agentSdkTypes.ts         # Agent SDK type definitions (~13 KB)
    │   ├── sandboxTypes.ts          # Sandbox type definitions (~6 KB)
    │   └── sdk/                    # SDK sub-entrypoints
    │
    ├── tools/                       # 40+ individual agent tool implementations
    ├── services/                    # Backend services (MCP, OAuth, analytics, autoDream, etc.)
    ├── components/                  # React/Ink terminal UI components
    ├── screens/                     # Full-screen Ink views (REPL, etc.)
    ├── state/                       # App state management (AppState, Zustand-style store)
    ├── commands/                    # Slash-command handler modules
    ├── coordinator/                 # Multi-agent orchestration (Swarm)
    ├── bridge/                      # Remote Control / IDE integration layer
    ├── buddy/                       # Tamagotchi-style companion system (18 species, gacha)
    ├── assistant/                   # Assistant-mode logic
    ├── bootstrap/                   # Module-level session singletons
    ├── cli/                         # CLI utilities
    ├── constants/                   # Shared constants
    ├── context/                     # Context-building sub-modules
    ├── hooks/                       # React hooks
    ├── ink/                         # Ink framework helpers
    ├── keybindings/                 # Keyboard binding definitions
    ├── memdir/                      # Memory/dream consolidation
    ├── migrations/                  # Config/data migration scripts
    ├── moreright/                   # Additional right-panel UI
    ├── native-ts/                   # Native TypeScript bindings
    ├── outputStyles/                # Terminal output style definitions
    ├── plugins/                     # Plugin system
    ├── query/                       # Query sub-modules
    ├── remote/                      # Remote session support
    ├── schemas/                     # Zod/JSON schema definitions
    ├── skills/                      # Skill system
    ├── tasks/                       # Task sub-modules
    ├── types/                       # Global TypeScript type declarations
    ├── upstreamproxy/               # Upstream proxy support
    ├── utils/                       # Utility functions
    ├── vim/                         # Vim-mode keybinding support
    └── voice/                       # Voice mode (push-to-talk)
```

---

## Notable Files

| File | Size | Role |
|------|------|------|
| `src/main.tsx` | ~785 KB | Full Commander.js CLI — the largest single file |
| `src/query.ts` | ~69 KB | Streaming LLM API loop and tool-call processing |
| `src/QueryEngine.ts` | ~47 KB | High-level session orchestrator |
| `src/interactiveHelpers.tsx` | ~57 KB | Interactive REPL helpers |
| `src/Tool.ts` | ~29 KB | Tool interface and utilities |
| `src/commands.ts` | ~25 KB | Slash-command implementations |
| `src/dialogLaunchers.tsx` | ~22 KB | Modal/dialog launchers |
| `src/setup.ts` | ~21 KB | Initialization logic |
| `src/tools.ts` | ~17 KB | Tool registry |
