# retro

Agent skill `/retro`: retrospective on a coding session that suggests improvements to the agent's environment. Part of OneStack.

## What it does

Reads the transcript of a coding session (the current one by default) and looks for candidates that would make future runs better: navigation pointers, automated checks and guardrails, coding standards for the reviewer agent, oversized or no-op steering files, expensive tool calls, and missing access to information. It presents the candidates ranked by severity; it does not apply them.

Based on Matt Pocock's `retro` skill ([mattpocock/skills](https://github.com/mattpocock/skills) at `4ae2317`, 2026-10-31, MIT), adapted to the Skill Standard: the `writing-for-agents` guide is bundled in `references/` instead of called as another skill, and harness-specific paths live in *Harness notes*.

## Usage

```text
/retro
/retro yesterday's session on the billing refactor
```

Output: a list of improvement candidates, most severe first, each tied to what happened in the session and its category.

## Install

From OneStack: `./sync-install.sh` — or copy this folder as `<skills dir>/retro/`.

## Requirements

None.

## Layout

- `SKILL.md` — the retro steps, categories and reference on implementation vs review.
- `references/writing-for-agents.md` — writing style guide for documents agents read.
- `references/skill-mechanics.md` — the skill-specific part of that guide.

## License

MIT — see `LICENSE`. Original work by Matt Pocock.
