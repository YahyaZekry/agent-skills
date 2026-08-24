# Claude Code Skills

A collection of custom skills for [Claude Code](https://claude.ai/code) — the AI coding CLI by Anthropic.

General-purpose skills only. For Obsidian-specific skills (research, note-linking, vault health checks), see the sibling repo: [claude-code-obsidian-skills](https://github.com/YahyaZekry/claude-code-obsidian-skills).

## Available Skills

> `project-knowledge` now lives in its own repo: [claude-code-project-knowledge](https://github.com/YahyaZekry/claude-code-project-knowledge).

| Skill | Description | Install |
|-------|-------------|---------|
| [concise-mode](skills/concise-mode/SKILL.md) | Reduces token usage ~75% by stripping filler — narration, preambles, closings — without touching reasoning, tool use, or code | [Download](concise-mode.skill) |
| [code-craft](skills/code-craft/SKILL.md) | Minimal-code mode — write the least code that correctly solves the task, match the surrounding abstraction level, and stop before adding what wasn't asked for | [Download](code-craft.skill) |

## Installation

### Option 1 — Install from this repo
```bash
# Clone the repo
git clone https://github.com/YahyaZekry/claude-code-skills.git

# Install a skill (from the repo root)
claude skill install ./claude-code-skills/concise-mode.skill
```

### Option 2 — Direct download
Download the `.skill` file and install:
```bash
claude skill install concise-mode.skill
```

## Usage

Once installed, invoke a skill by name in Claude Code:

```
concise mode
```
```
code-craft
```

## Skills Overview

### `concise-mode`
Reduces token usage ~75% by stripping verbal filler — without changing how Claude reasons, uses tools, or handles code. Cuts narration before/after tool calls and agent spawning, preambles, and closings. The answers stay identical; you just stop paying for the wrapper around them.

**Trigger:** `concise mode`
**Deactivate:** `verbose mode` or `deactivate concise`

---

### `code-craft`
Minimal-code mode. Writes the least code that correctly solves the task, matches the surrounding codebase's abstraction level, and stops before adding anything that wasn't asked for. Stays active for the rest of the conversation. Also works on existing code — share something over-built and ask for it simpler.

**Trigger:** `code-craft`, `minimal mode`, `keep it simple`, `stop over-engineering`, `simplify this`
**Deactivate:** `code-craft off`

---

## Contributing

PRs welcome. Each skill lives in `skills/<name>/SKILL.md`. The `.skill` files at the root are the packaged installable versions (ZIP archives).
