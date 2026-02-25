# ai-dev-prompts

Curated AI prompts for developer workflows — context file generation, code review, refactoring, and more. Built for teams standardizing on AI-assisted development.

## The Problem

Every major AI coding tool has its own project context file. Same purpose, different locations:

| Tool | File | Location |
|---|---|---|
| Cursor (legacy) | `.cursorrules` | Project root |
| Cursor (new) | `.cursor/rules/*.mdc` | `.cursor/rules/` directory |
| Claude Code CLI | `CLAUDE.md` | Project root |
| Gemini CLI | `GEMINI.md` | Project root |
| Google Antigravity | `.agent/rules/*.md` | `.agent/rules/` directory |
| GitHub Copilot | `.github/copilot-instructions.md` | `.github/` directory |
| Windsurf | `.windsurfrules` | Project root |
| Cline | `.clinerules` | Project root |
| OpenAI Codex | `AGENTS.md` | Project root |
| Zed | `.zed/settings.json` | `.zed/` directory |

**The content is ~90% the same across all tools.** The investment in one context file is reusable — copy content, change file name.

**Only ~30% of developers use context files.** This is the single biggest gap between "fancy autocomplete" and genuinely productive AI-assisted development.

## What's in This Repo

### Context File Generators (`context-files/`)

Prompts that scan your existing codebase and auto-generate the right context file. No manual writing required — paste the prompt, review the output, commit.

| Prompt | Tool | Type |
|---|---|---|
| [cursorrules-small-repo.md](context-files/cursorrules-small-repo.md) | Cursor | Single `.cursorrules` file for standard repos |
| [cursorrules-large-repo.md](context-files/cursorrules-large-repo.md) | Cursor | Modular `.cursor/rules/*.mdc` for complex/monorepos |
| [claude-md-generator.md](context-files/claude-md-generator.md) | Claude Code CLI | `CLAUDE.md` — terminal agent onboarding docs |
| [gemini-rules-generator.md](context-files/gemini-rules-generator.md) | Gemini CLI | `GEMINI.md` — terminal agent onboarding docs |
| [antigravity-rules-generator.md](context-files/antigravity-rules-generator.md) | Google Antigravity | `.agent/rules/*.md` with activation modes |

### Coming Soon

- **Copilot instructions generator** — `.github/copilot-instructions.md`
- **Windsurf / Cline generators** — `.windsurfrules`, `.clinerules`
- **Codex agent generator** — `AGENTS.md`
- **Workflow prompts** — code review, migration, refactoring, debugging
- **Output templates** — starter templates for each context file format

## Quick Start

1. Pick the prompt that matches your AI tool
2. Open the prompt file and copy the code block
3. Paste it into your AI tool (Cursor Agent mode, Claude Code, Gemini CLI, etc.)
4. **Use the best model available** — Claude Sonnet/Opus, GPT-4o, or Gemini 2.5 Pro
5. Review the output — verify stack versions, conventions, and security rules
6. Commit the generated file to git

## Which Prompt Should I Use?

```
Are you using an IDE or a terminal agent?

IDE (Cursor, Antigravity, VS Code)
├── Simple/single repo? → cursorrules-small-repo.md
├── Complex/monorepo?   → cursorrules-large-repo.md
└── Antigravity?        → antigravity-rules-generator.md

Terminal agent (CLI)
├── Claude Code?  → claude-md-generator.md
└── Gemini CLI?   → gemini-rules-generator.md
```

## Design Principles

These prompts are designed to:

1. **Auto-generate from existing code** — scan the repo, don't start from scratch
2. **Never guess** — if something can't be determined, write "Unknown" instead of hallucinating
3. **Enforce security defaults** — credential exclusion, protected areas, destructive command warnings
4. **Be copy-paste ready** — no customization needed for basic setup
5. **Work across stacks** — JS/TS, Python, Ruby, Go, Java, .NET, and more

## Contributing

This repo is a living collection. Contributions are welcome and encouraged.

### Ways to Contribute

- **Improve existing prompts** — found a section that could be clearer, or a case the prompt misses? Open a PR.
- **Add new tool generators** — Windsurf, Cline, Codex, Copilot, Zed — all need prompts.
- **Add workflow prompts** — code review, migration planning, refactoring, debugging workflows.
- **Share your generated output** — if you ran a prompt and the result was great (or terrible), share it as an example so others can learn.
- **Report issues** — if a prompt hallucinates versions, misses conventions, or generates weak security rules, open an issue.

### How to Contribute

1. Fork this repo
2. Create a branch (`git checkout -b add-windsurf-generator`)
3. Add or edit your prompt file
4. Open a PR with a brief description of what changed and why

### What Makes a Good Prompt

- **Grounded in repo contents** — never asks the AI to guess or assume
- **Security-first** — always includes protected areas for secrets, auth, CI/CD
- **Tool-aware** — accounts for what the specific tool can and can't do
- **Concise output** — the generated context file should be useful, not exhaustive

## References

- [awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules) — 130+ community .cursorrules examples
- [ai-prompts by instructa](https://github.com/instructa/ai-prompts) — curated prompts for Cursor, Cline, Windsurf, Copilot
- [Cursor rules docs](https://docs.cursor.com/context/rules-for-ai) — official `.cursor/rules/` documentation
- [Gemini CLI docs](https://github.com/google-gemini/gemini-cli) — GEMINI.md and CLI reference
- [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code) — CLAUDE.md and CLI reference
- [Google Antigravity](https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/) — Antigravity platform overview

## License

MIT
