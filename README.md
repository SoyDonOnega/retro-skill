# retro

Agent skill `/retro`: retrospective on an agent session that suggests improvements to the agent's environment. Part of OneStack.

## What it does

Reads the transcript of an agent session (the current one by default; coding, writing or research) and looks for candidates that would make future runs better: navigation pointers, automated checks and guardrails, rules for whatever review stage exists, oversized or no-op steering files, expensive tool calls, missing access to information, and persistent-memory entries to add or remove. It presents the candidates in one table ranked by severity, with the context cost of each; it does not apply them.

Based on Matt Pocock's `retro` skill ([mattpocock/skills](https://github.com/mattpocock/skills) at `4ae2317`, 2026-10-31, MIT), adapted to the Skill Standard: the `writing-for-agents` guide is bundled in `references/` instead of called as another skill, and harness-specific paths live in *Harness notes*.

### Changes from upstream

Kept here so an upstream sync can be merged by hand (setup review, 2026-11-01):

1. **Review stage detected, not assumed.** New step 3 maps what reviews the work (a review command, a reviewer agent, human PR review, or none); "Coding standards" became *Review rules* aimed at that stage, and having none is a finding.
2. **Sources of truth.** Candidates target the original of any installed or copied file (repo, synced folder, install script), never the copy.
3. **Any agent session.** Not only coding sessions; *Automated checks* applies when the session touched code.
4. **Fixed output.** One table: severity, category, what happened, proposed change, target, context cost (tokens added per turn when always-loaded).
5. **Persistent memory.** New category: recurring corrections become candidate memory entries; wrong or stale memories become candidates for removal.

## Usage

```text
/retro
/retro yesterday's session on the billing refactor
```

Output: one table of improvement candidates, most severe first: severity, category, what happened in the session, proposed change, target file (the original, not an installed copy) and context cost.

## Install

From OneStack: `./sync-install.sh` — or copy this folder as `<skills dir>/retro/`.

## Requirements

None.

## Layout

- `SKILL.md` — the retro steps, categories, output table and reference on implementation vs review.
- `references/writing-for-agents.md` — writing style guide for documents agents read.
- `references/skill-mechanics.md` — the skill-specific part of that guide.

## License

MIT — see `LICENSE`. Original work by Matt Pocock.
