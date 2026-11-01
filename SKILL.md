---
name: retro
description: >-
  Conducts a retrospective on an agent session (coding or not) and suggests improvements
  to the agent's environment (navigation pointers, automated checks, review rules, steering
  files, tool economy, information access, persistent memory), ranked by severity with
  their context cost. Use when the user runs /retro [session].
license: MIT
argument-hint: "[session]"
disable-model-invocation: true
---

# Retro

The user has asked for a **retrospective**. You are suggesting improvements to the agent's **environment** to improve future runs. The skill does not apply the improvements: it presents candidates, and the user decides.
Paths in this file are relative to the skill root (the folder that contains this SKILL.md).

## When invoked

1. Read `references/writing-for-agents.md` for the writing style guide.

2. Read the primary sources for the session the user specifies. This may mean searching through the session transcripts the agent harness keeps on this machine. If the user does not specify a session, default to the current one. The session may be coding, writing, research or any other agent work.

3. Map the environment before judging it:
   - **Review stage**: what reviews the work here? A review command or skill, a reviewer agent, human review of pull requests, or nothing. Candidates that need a reviewer target the stage that exists; none at all is itself a finding.
   - **Sources of truth**: for every steering file, skill or config that is installed or copied from somewhere else (a repo, a synced folder, an install script), find the original. Candidates edit the original, never the installed copy.

4. Look for candidates for improvement in these categories.

- **Navigation**: how easy was it for the agent to find the right files or notes? Are there hidden dependencies between files? Would a **navigation pointer** make it easier? _Use when_ the session took a long time to find a piece of information.
- **Automated checks** (sessions that touched code): are there automated checks that could catch errors the agent made? Linting, typing, tests, filesystem linters? Read the repo's own check command first (its `package.json`/build-tool `lint`/`check` scripts, its CI workflow), so a check that already exists but sits unwired or silently broken is the finding, not a reinvention. A repo with no **guardrail** (no pre-commit hook and no CI job running its lint/typecheck/test command) is itself a finding: an un-linted repo is a standing missed opportunity, not a neutral default. _Use when_ the agent made a mistake an automated check could have caught, or the repo has no guardrail at all.
- **Review rules**: should the review stage found in step 3 enforce a new rule, or drop or clarify one? Classify the violation first: a **mechanical** one (a fixed syntactic pattern, a banned API, an import shape, a file-location rule) gets a deterministic check, full stop: a custom rule in the repo's own linter, a new pre-commit hook, or a new CI job, whichever the repo's language and existing guardrail make cheapest. Default to building the check over writing the rule. Reserve the review stage's rules file (whatever it reads, e.g. `CODING_STANDARDS.md`) for genuine **judgement calls** (cross-file consistency, "matches the surrounding style," anything no guardrail could ever substitute for). _Use when_ the review stage failed to catch a mistake.
- **Global steering files**: are there any steering instructions that should be moved to review rules (or automated checks) instead? _Use when_ the agent instruction file is particularly large, in the project OR the user's global scope.
- **Tool economy**: did the agent make expensive tool calls that could be streamlined? Is there any custom tooling (CLIs, MCP servers) that is particularly token-inefficient? _Use when_ the agent made an expensive tool call.
- **No-ops**: look for instructions in steering files that do not modify the agent's behavior. _Use when_ the steering files are large and unwieldy.
- **Information access**: look for opportunities to increase the agent's access to information. Teeing dev server logs, readonly access to third-party services or connectors. _Use when_ a crucial piece of information was not available to the agent.
- **Persistent memory**: a correction or preference the user gave in the session that will recur is a candidate memory entry; a memory the session proved wrong or stale is a candidate for removal. _Use when_ the harness keeps a persistent memory and the user corrected the agent.

5. Present the candidates as one table, most severe first, in the user's language:

| # | Severity | Category | What happened (step or tool call) | Proposed change | Target (original file) | Context cost |
|---|---|---|---|---|---|---|

   - **Severity**: high (caused a wrong result or a lot of rework), medium (cost time or tokens), low (friction).
   - **Context cost**: the approximate tokens the change adds to every turn when it lands in always-loaded material (a steering file line, a skill description, a memory index line); `0` when it is reached only through a pointer, a check or a script.

## Rules

- Suggest; do not edit steering files, checks, rules or memory during the retro unless the user asks.
- Ground every candidate in something that happened in the session (a step, a tool call, a mistake); no generic advice.
- Target the original of any installed or copied file, never the copy.

## Reference

### Implementation vs Review

When the work goes through two stages, implementation and review, the implementation agent has the most **context pressure**: it is responsible for exploration, writing code, and debugging failures. The review stage has the least: it receives a diff, so no exploration is needed, and it often does not need to write code or debug.

So the review stage, not the implementation agent, should impose standards. Where step 3 found no review stage, say so in a candidate instead of assuming one.

### Files

- Agent instruction files (`AGENTS.md`, or the harness's own equivalent): these files are pushed to the context window of any agent working in this project. They should be used incredibly sparingly, usually only for **navigation pointers** to other files.
- The review stage's rules file (e.g. `CODING_STANDARDS.md`), when one exists: read during review, not implementation. Add **navigation pointers** to docs folders if it gets more than 1,000 lines long.
- Docs: use docs as references files, pointed to by other files. Look for existing docs before writing new ones.
- Skills: use skills for docs (since their description goes into the agent's context window), or for user-invoked commands. Follow the advice in `references/writing-for-agents.md` and `references/skill-mechanics.md`.
- Persistent memory, when the harness keeps one: one fact per entry; an index line costs context every session.

## Resources

- `references/writing-for-agents.md` — writing style guide for any document an agent reads (context pointers, information hierarchy, leading words, pruning).
- `references/skill-mechanics.md` — what changes when the document is a skill (invocation choice, router skills).

## Harness notes

- Session transcripts: Claude Code keeps them as JSONL under `~/.claude/projects/<project-path>/`; Codex under `~/.codex/sessions/`. Other harnesses: look for their session or history folder.
- Agent instruction files: Claude Code reads `CLAUDE.md`; most other harnesses read `AGENTS.md`.
- Review stage in Claude Code: the `/code-review` command, or human review of the pull request.
- Persistent memory in Claude Code: the memory folder and its `MEMORY.md` index named in the system prompt.
