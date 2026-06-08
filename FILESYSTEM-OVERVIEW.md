# Filesystem Overview

- Date: 2026/06/07
- Summary: An archival mirror of the leaked source code of Anthropic's official Claude Code CLI, recovered from a sourcemap (`.map`) file accidentally published to the npm registry (per the README). The codebase is a TypeScript/TSX application that builds a terminal-based AI coding agent: a Commander.js CLI entrypoint, a custom React/Ink terminal renderer, 40+ agent tools (Bash, file ops, web, MCP), multi-agent orchestration, IDE bridges, and assorted internal subsystems (the "BUDDY" Tamagotchi companion, a "Dream" memory-consolidation service, KAIROS/ULTRAPLAN planning, undercover mode). The repo ships only source plus a few image assets — there is no build/dependency manifest.

## Structure

```
yasasbanuaofficial/
├── README.md                       — Story of the leak + architecture tour and explore instructions
├── .git/                           — Git version-control metadata   (dot-folder leaf, not expanded)
├── assets/                         — Images used by the README   (expanded)
│   ├── claude-logo.png             — Claude branding logo image
│   ├── claude-npm-img.png          — Screenshot of leaked source files visible in the npm package
│   └── x-post.png                  — Screenshot of the tweet announcing the leak
└── src/                            — Entire decompiled Claude Code source tree (~1900 files)   (expanded)
│   ├── commands.ts                 — Registry/loader of built-in slash commands
│   ├── context.ts                  — Builds system/user context (git status, date, CLAUDE.md) for API calls
│   ├── cost-tracker.ts             — Tracks token/dollar cost across a session
│   ├── costHook.ts                 — Hook wiring cost tracking into the query loop
│   ├── dialogLaunchers.tsx         — Helpers that open the various interactive Ink dialogs
│   ├── history.ts                  — Conversation/session history persistence and retrieval
│   ├── ink.ts                      — Ink render wrapper with theme injection
│   ├── interactiveHelpers.tsx      — Shared helpers for the interactive REPL flow
│   ├── main.tsx                    — Primary CLI definition (Commander.js + React/Ink), ~800KB
│   ├── projectOnboardingState.ts   — Tracks first-run/onboarding state per project
│   ├── query.ts                    — Core API query loop: streaming, tool-call handling, turn loop
│   ├── QueryEngine.ts              — Higher-level orchestrator wrapping query() (state, compaction, history)
│   ├── replLauncher.tsx            — Bootstraps and launches the interactive REPL screen
│   ├── setup.ts                    — One-time setup/initialization routines
│   ├── Task.ts                     — Base task type definitions
│   ├── tasks.ts                    — Task registry/utilities
│   ├── Tool.ts                     — Base Tool interface and tool-lookup utilities
│   ├── tools.ts                    — Tool registry assembling the active tool list
│   ├── assistant/                  — Assistant-side session history helpers
│   │   └── sessionHistory.ts       — Assistant session history management
│   ├── bootstrap/                  — Module-level session-global singletons
│   │   └── state.ts                — Session ID, CWD, project root, token counts, model overrides
│   ├── bridge/                     — Remote Control / IDE bridge mode (sessions, JWT auth, messaging)
│   │   ├── bridgeApi.ts            — Bridge HTTP/RPC API surface
│   │   ├── bridgeMain.ts           — Bridge mode entrypoint
│   │   ├── replBridge.ts           — Bridges the local REPL to a remote controller
│   │   ├── jwtUtils.ts             — JWT signing/verification for bridge auth
│   │   └── … (~27 more)            — Session creation/runner, polling, attachments, trusted devices
│   ├── buddy/                      — The "BUDDY" terminal Tamagotchi companion system
│   │   ├── companion.ts            — Companion state/logic (gacha PRNG, species, stats)
│   │   ├── CompanionSprite.tsx     — Renders the companion sprite in the terminal
│   │   ├── sprites.ts              — Sprite art definitions
│   │   ├── prompt.ts               — Prompt text for generating companion "souls"
│   │   ├── types.ts                — Companion type definitions
│   │   └── useBuddyNotification.tsx— Hook surfacing companion notifications
│   ├── cli/                        — Non-interactive CLI plumbing (print, transports, handlers)
│   │   ├── exit.ts                 — Process exit handling
│   │   ├── ndjsonSafeStringify.ts  — Safe NDJSON serialization
│   │   ├── print.ts                — Print/headless output formatting
│   │   ├── remoteIO.ts             — Remote I/O plumbing
│   │   ├── structuredIO.ts         — Structured (JSON) I/O for SDK/pipe mode
│   │   ├── update.ts               — Self-update command logic
│   │   ├── handlers/               — Subcommand handlers (agents, auth, autoMode, mcp, plugins)
│   │   └── transports/             — Event transports (WebSocket, SSE, hybrid, uploaders)
│   ├── commands/                   — ~80 slash-command implementations (one folder/file each)
│   │   ├── advisor.ts              — /advisor command
│   │   ├── commit.ts               — /commit command
│   │   ├── commit-push-pr.ts       — Combined commit+push+PR command
│   │   ├── brief.ts                — /brief command
│   │   ├── install.tsx             — Install/setup command UI
│   │   ├── insights.ts             — /insights command
│   │   └── … (agents/, config/, doctor/, login/, mcp/, model/, memory/, …) — Per-command dirs
│   ├── components/                 — ~390 React/Ink UI components for the terminal
│   │   ├── App.tsx                 — Root provider component
│   │   ├── Messages.tsx            — Conversation message list rendering
│   │   ├── MessageRow.tsx          — Single message row renderer
│   │   ├── Onboarding.tsx          — First-run onboarding UI
│   │   ├── agents/                 — Agent editor/creation wizard components
│   │   ├── permissions/            — Tool-permission approval dialogs
│   │   ├── design-system/          — Reusable UI primitives (Dialog, FuzzyPicker, ThemeProvider)
│   │   ├── messages/               — Per-message-type renderers
│   │   ├── tasks/                  — Background-task/agent status dialogs
│   │   └── … (Settings/, Spinner/, HelpV2/, FeedbackSurvey/, sandbox/, skills/, …)
│   ├── constants/                  — Shared constant tables (limits, prompts, betas, figures)
│   │   ├── apiLimits.ts            — API rate/size limits
│   │   ├── prompts.ts              — Canned prompt strings
│   │   ├── systemPromptSections.ts — System-prompt section definitions
│   │   ├── tools.ts                — CORE_TOOLS whitelist constants
│   │   └── … (~17 more)            — betas, errorIds, keys, messages, oauth, product, etc.
│   ├── context/                    — React context providers for app-wide state
│   │   ├── stats.tsx               — Stats context
│   │   ├── mailbox.tsx             — Inter-session mailbox context
│   │   ├── notifications.tsx       — Notifications context
│   │   └── … (modal, overlay, voice, fpsMetrics, QueuedMessage)
│   ├── coordinator/                — Multi-agent (swarm) orchestration
│   │   └── coordinatorMode.ts      — Coordinator/swarm mode logic
│   ├── entrypoints/                — Process entrypoints and SDK type surface
│   │   ├── cli.tsx                 — True CLI entrypoint with fast-path command routing
│   │   ├── init.ts                 — One-time initialization (telemetry, config, trust)
│   │   ├── mcp.ts                  — MCP server entrypoint
│   │   ├── agentSdkTypes.ts        — Agent SDK type definitions
│   │   ├── sandboxTypes.ts         — Sandbox type definitions
│   │   └── sdk/                    — SDK control/core schemas and types
│   ├── hooks/                      — ~100 React hooks for REPL behavior (input, IDE, suggestions)
│   │   ├── useCanUseTool.tsx       — Tool-permission gating hook
│   │   ├── usePasteHandler.ts      — Clipboard/paste handling
│   │   ├── useRemoteSession.ts     — Remote-session lifecycle hook
│   │   ├── notifs/                 — Notification hooks
│   │   ├── toolPermission/         — Tool-permission hook helpers
│   │   └── … (~95 more)            — IDE integration, suggestions, keybindings, history, etc.
│   ├── ink/                        — Custom forked Ink terminal renderer (layout, reconciler, termio)
│   │   ├── ink.tsx                 — Ink render entry
│   │   ├── reconciler.ts           — React reconciler for terminal nodes
│   │   ├── renderer.ts             — Frame renderer
│   │   ├── components/             — Built-in Ink components
│   │   ├── hooks/                  — Ink-level hooks
│   │   ├── layout/                 — Layout engine integration
│   │   ├── events/                 — Input/event handling
│   │   └── termio/                 — Low-level terminal I/O
│   ├── keybindings/                — Keybinding parsing, matching, and user-binding loading
│   │   ├── parser.ts               — Keybinding-string parser
│   │   ├── resolver.ts             — Resolves bindings to actions
│   │   ├── defaultBindings.ts      — Built-in default keymap
│   │   └── … (schema, validate, template, useKeybinding, etc.)
│   ├── memdir/                     — Durable memory directory ("Dream"/MEMORY.md) management
│   │   ├── memdir.ts               — Memory-directory core
│   │   ├── findRelevantMemories.ts — Retrieves relevant memories
│   │   ├── memoryScan.ts           — Scans memory files
│   │   └── … (memoryAge, memoryTypes, paths, teamMem*)
│   ├── migrations/                 — One-shot settings/model migrations run at startup
│   │   ├── migrateFennecToOpus.ts  — Migrate old model alias to Opus
│   │   ├── migrateSonnet45ToSonnet46.ts — Model-version migration
│   │   └── … (~9 more migrations)
│   ├── moreright/                  — UI hook for "more right" overflow affordance
│   │   └── useMoreRight.tsx        — Hook detecting right-overflow content
│   ├── native-ts/                  — Pure-TS reimplementations of native modules
│   │   ├── color-diff/             — Color-difference computation
│   │   ├── file-index/             — File-index implementation
│   │   └── yoga-layout/            — Yoga flexbox layout in TS
│   ├── outputStyles/               — Output-style directory loading
│   │   └── loadOutputStylesDir.ts  — Loads user output-style definitions
│   ├── plugins/                    — Plugin system + bundled plugins
│   │   ├── builtinPlugins.ts       — Registry of built-in plugins
│   │   └── bundled/                — Bundled plugin definitions
│   ├── query/                      — Query-loop configuration and helpers
│   │   ├── config.ts               — Query config
│   │   ├── deps.ts                 — Query dependency wiring
│   │   ├── stopHooks.ts            — Stop-condition hooks
│   │   └── tokenBudget.ts          — Token-budget accounting
│   ├── remote/                     — Remote-session management (WebSocket, permission bridge)
│   │   ├── RemoteSessionManager.ts — Manages remote agent sessions
│   │   ├── SessionsWebSocket.ts    — WebSocket transport for sessions
│   │   ├── remotePermissionBridge.ts — Forwards permission prompts to remote
│   │   └── sdkMessageAdapter.ts    — Adapts SDK messages for remote transport
│   ├── schemas/                    — Shared zod/JSON schemas
│   │   └── hooks.ts                — Hook config schema
│   ├── screens/                    — Top-level Ink screens
│   │   ├── REPL.tsx                — Interactive REPL screen
│   │   ├── Doctor.tsx              — Diagnostics ("doctor") screen
│   │   └── ResumeConversation.tsx  — Session-resume picker screen
│   ├── server/                     — Direct-connect local session server
│   │   ├── directConnectManager.ts — Manages direct-connect sessions
│   │   ├── createDirectConnectSession.ts — Session factory
│   │   └── types.ts                — Server type definitions
│   ├── services/                   — ~130 backend services (API, MCP, OAuth, Dream, memory)
│   │   ├── api/                    — Anthropic/3rd-party API client layer
│   │   ├── mcp/                    — MCP client/server services
│   │   ├── oauth/                  — OAuth flows
│   │   ├── autoDream/              — Background "Dream" memory-consolidation subagent
│   │   ├── extractMemories/        — Memory-extraction service
│   │   ├── lsp/                    — Language-server integration
│   │   ├── analytics/              — Analytics (largely empty in leak)
│   │   └── … (compact/, AgentSummary/, PromptSuggestion/, voice*, etc.)
│   ├── skills/                     — Skill system + bundled skills
│   │   ├── bundledSkills.ts        — Registry of bundled skills
│   │   ├── loadSkillsDir.ts        — Loads user skills from disk
│   │   ├── mcpSkillBuilders.ts     — Builds MCP-backed skills
│   │   └── bundled/                — Built-in skill definitions (verify, simplify, loop, etc.)
│   ├── state/                      — Central app-state store and selectors
│   │   ├── AppState.tsx            — App-state type + context provider
│   │   ├── store.ts                — State store factory
│   │   ├── selectors.ts            — State selectors
│   │   └── … (AppStateStore, onChangeAppState, teammateViewHelpers)
│   ├── tasks/                      — Task-type implementations (agents, shells, dreams, teammates)
│   │   ├── LocalMainSessionTask.ts — Main local session task
│   │   ├── pillLabel.ts            — Task pill-label rendering
│   │   ├── DreamTask/              — Dream (memory) background task
│   │   ├── LocalAgentTask/         — Local subagent task
│   │   ├── LocalShellTask/         — Background shell task
│   │   ├── RemoteAgentTask/        — Remote agent task
│   │   └── InProcessTeammateTask/  — In-process teammate task
│   ├── types/                      — Shared TypeScript type definitions
│   │   ├── permissions.ts          — Permission-mode/result types
│   │   ├── hooks.ts                — Hook types
│   │   ├── command.ts              — Command types
│   │   ├── plugin.ts               — Plugin types
│   │   ├── … (ids, logs, textInputTypes)
│   │   └── generated/              — Codegen'd protobuf/event types (Google protobuf, event_mono)
│   ├── tools/                      — 40+ agent tool implementations (one folder per tool)
│   │   ├── utils.ts                — Shared tool utilities
│   │   ├── BashTool/               — Shell-execution tool
│   │   ├── FileEditTool/           — File-edit tool
│   │   ├── FileReadTool/           — File-read tool
│   │   ├── GrepTool/, GlobTool/    — Search tools
│   │   ├── WebFetchTool/, WebSearchTool/ — Web tools
│   │   ├── MCPTool/, McpAuthTool/  — MCP tools
│   │   ├── AgentTool/, Task*Tool/  — Sub-agent / task tools
│   │   └── … (LSPTool, REPLTool, SkillTool, shared/, testing/, etc.)
│   ├── upstreamproxy/              — Upstream API proxy/relay
│   │   ├── upstreamproxy.ts        — Proxy server
│   │   └── relay.ts                — Request relay logic
│   ├── utils/                      — ~560 utility modules (auth, shell, git, model, undercover)
│   │   ├── auth.ts                 — Authentication helpers
│   │   ├── Shell.ts                — Shell abstraction
│   │   ├── claudemd.ts             — CLAUDE.md discovery/loading
│   │   ├── api.ts                  — API helpers
│   │   ├── undercover.ts           — "Undercover mode" blocking internal info leakage
│   │   ├── bash/, background/, claudeInChrome/ — Sub-utility areas
│   │   └── … (~550 more)           — Model providers, betas, billing, caching, parsing, etc.
│   ├── vim/                        — Vim-mode text editing primitives
│   │   ├── motions.ts              — Vim motion commands
│   │   ├── operators.ts            — Vim operator commands
│   │   ├── textObjects.ts          — Vim text objects
│   │   └── … (transitions, types)
│   └── voice/                      — Voice-mode enablement gate
│       └── voiceModeEnabled.ts     — Feature gate for voice input
```
