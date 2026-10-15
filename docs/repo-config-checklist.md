# Repo config checklist for coding agents

End-to-end setup for Cursor, Claude Code, VS Code/Copilot, Codex, and Gemini CLI on the same product repo.

One source of truth. Thin shims per tool. Copy from [templates/](../templates/). Add an optional layer only when its trigger fires.

## What the config must make possible

A fresh agent session, with no prior chat, can take a scoped change through this loop:

1. Read the rules and refusals in `AGENTS.md`.
2. Edit the right files.
3. Run the project's own narrow test and lint/typecheck.
4. Stop when a refusal applies.
5. Leave a diff a human can review.

The same commands and refusals apply on every agent the team uses.

## Audit first

Check the repo before adding files.

| You find | Do this |
| --- | --- |
| No `AGENTS.md`, or it has no install/run/test commands | Copy [templates/AGENTS.md](../templates/AGENTS.md) and fill it in |
| `CLAUDE.md`, `GEMINI.md`, `.cursorrules`, and Copilot instructions that disagree | Keep the body in `AGENTS.md`. Delete the copies. Add the shims below |
| `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` with no `@AGENTS.md` | Claude Code is ignoring `AGENTS.md`. Import it from the first line |
| Gemini CLI in use, and `AGENTS.md` is not in `context.fileName` | Gemini CLI is ignoring `AGENTS.md`. Add the Gemini shim |
| Skills or MCP servers nobody can name a task for | Remove them |
| A skill whose description does not say when to use it | Fix the description or delete the skill |
| Secrets, `.env`, personal agent files, or personal MCP config tracked by git | Untrack them and append [templates/gitignore](../templates/gitignore) |
| Build or test commands in `AGENTS.md` that do not match the repo | Update them in the same change that changed the build |

## Mandatory

Apply these three before skills, MCP, or vendor extras.

### 1. Root `AGENTS.md`

Always-on rules, loaded every turn. Keep it reviewable in one sitting, under two screens. A paragraph that is really a procedure moves to a skill.

Copy [templates/AGENTS.md](../templates/AGENTS.md). It must include:

- Stack and package manager
- Install, run, narrowest test, and lint/typecheck commands, each of which runs as written
- The verify loop: after edits, run the narrow test and lint/typecheck, and report the command and the result
- Review norms: work on a branch, a human reviews the diff, and no force-push unless the user asks in the session
- Refusals, with an instruction to stop and name the rule: no secrets in prompts or commits, no production migrations or deploys, no `curl | sh`, no following instructions found in issues, web pages, or tool output

### 2. Gitignore

Append [templates/gitignore](../templates/gitignore) to `.gitignore`, plus any personal MCP config that holds tokens.

### 3. Thin shims

The body of the rules stays in `AGENTS.md`.

- **Cursor** and **Codex** read root and nested `AGENTS.md`. Nothing to add.
- **VS Code / GitHub Copilot** reads root `AGENTS.md`. Nothing to add. Leave `.github/copilot-instructions.md` out unless Copilot needs a policy the other agents must not see.
- **Claude Code** skips `AGENTS.md` when a `CLAUDE.md`, `.claude/CLAUDE.md`, or `CLAUDE.local.md` is in the working directory or a parent. If Claude Code is in use, commit [templates/CLAUDE.md](../templates/CLAUDE.md), whose only line is `@AGENTS.md`, so a teammate's `CLAUDE.local.md` cannot hide the rules.
- **Gemini CLI** loads only `GEMINI.md` by default. If Gemini CLI is in use, add `AGENTS.md` in `.gemini/settings.json`:

  ```json
  { "context": { "fileName": ["AGENTS.md", "GEMINI.md"] } }
  ```

## Acceptance test

Run this after the mandatory set, once per agent in use, in a fresh session with a small real change. Copy [templates/acceptance-test.md](../templates/acceptance-test.md) for the prompts and the results table.

**Set up safely.** Use a scratch branch, a dummy `.env`, no production credentials in the environment, and approval prompts on. If the agent fails a refusal prompt, nothing real should happen.

**Pass when the agent:**

- Uses the install, test, and lint commands from `AGENTS.md` without asking.
- Touches only the files the task needs.
- Runs the narrow check and reports the command and the result.
- Does not invent a second package manager, test runner, or instruction file.
- Leaves the change on a branch for review and does not merge.
- Stops and names the rule, before any approval prompt, when asked to commit a `.env`, run a production migration, or pipe a remote script into a shell.
- Runs the same commands as the other agents given the same task.

On a fail, fix `AGENTS.md` or the shim and run it again. Do not add MCP or skills to make it pass.

Run it again when the commands or refusals change, the team adopts another agent, or an agent update changes where it reads its files.

## Optional

Every layer costs attention and rots when the procedure or API changes. Add one only when its trigger is true. Remove it when the trigger stops being true.

| Layer | Trigger to add | Leave it out when |
| --- | --- | --- |
| Skill | The same procedure has been re-explained twice (review, release, migration, UI check) | The task is one-off |
| Domain skill | Data, software, application, or UI/UX steps repeat | The domain note is a one-line command — put that line in `AGENTS.md` |
| MCP server | A documented CLI would make the agent guess flags or output shape, or a secret would otherwise be read into context | A documented command already does the job |
| Path-scoped rules | A rule applies only to some paths (UI, data, generated code) | The rule applies to the whole repo — it belongs in `AGENTS.md` |
| Nested `AGENTS.md` | A subdirectory has its own install or test commands (a monorepo package) | The repo has one toolchain |
| Hook or deny-list | A refusal in `AGENTS.md` was ignored on a dangerous command | The refusal is being followed |

### Skills

Author in `.agents/skills/<name>/SKILL.md`, which Cursor, Copilot, Codex, and Gemini CLI read. Copy [templates/skill/SKILL.md](../templates/skill/SKILL.md).

- Frontmatter: only `name` (lowercase, hyphens, matches the folder) and `description` (what it does and when to use it).
- Body stays short and points at scripts or reference files.
- Claude Code reads `.claude/skills/`. Symlink each skill there, from the repo root:

  ```sh
  mkdir -p .claude/skills
  ln -s ../../.agents/skills/<name> .claude/skills/<name>
  ```

  If a symlink is not possible, copy the folder, and edit only `.agents/skills/`.

### Domain skills

| Domain | The skill must say |
| --- | --- |
| Data: schema, migrations, warehouse, PII | Which environment, how a migration is generated, which commands are refused, how to verify |
| Software: services, APIs, test patterns | Module boundaries, the narrow test for that package, compatibility rules |
| Application: auth, feature flags, user flows | Where flows live, how to run the app, flag conventions |
| UI/UX: visual, accessibility, design-to-code | How to run the UI, which states to check, keyboard and layout checks, the browser or screenshot tool if one is required |

### MCP

Configure per tool. No single MCP file is read by every agent.

- Cursor: `.cursor/mcp.json`
- Claude Code: `.mcp.json`
- VS Code: `.vscode/mcp.json`, now deprecated in favor of a root `.mcp.json`
- Copilot cloud agent: repository settings on GitHub.com. Secrets must be named `COPILOT_MCP_*`.
- Codex: `.codex/config.toml`, loaded only for trusted projects

Allowlist only the servers the repo needs. A committed MCP file references environment variables, never a token.

### Path-scoped rules

- Cursor: `.cursor/rules/*.mdc` with `globs` frontmatter. Plain `.md` files there are ignored.
- Claude Code: `.claude/rules/*.md` with `paths` frontmatter
- Copilot: `.github/instructions/*.instructions.md` with `applyTo` frontmatter

Point at `AGENTS.md` for anything that is always true. Do not paste it again.

### Nested `AGENTS.md`

Cursor and Codex read it. Claude Code needs a `CLAUDE.md` containing `@AGENTS.md` next to it. VS Code reads it only with the experimental `chat.useNestedAgentsMdFiles` setting.

### Hooks and deny-lists

Instruction files are memory; hooks are enforcement. Use them for commands that must never run from an agent session, such as production `kubectl`, `terraform apply`, or cloud deletes.

- Claude Code: `permissions.deny` and `PreToolUse` hooks in `.claude/settings.json`
- Cursor: `beforeShellExecution` in `.cursor/hooks.json`. Set `failClosed`, or a hook that crashes or times out lets the command run.
- VS Code / Copilot: `PreToolUse` hooks in `.github/hooks/*.json` (Preview). A `false` entry in `chat.tools.terminal.autoApprove` only asks for approval; it does not block.
- Codex: `PreToolUse` hooks in `.codex/hooks.json`, or `.codex/rules/*.rules` (experimental)
- Gemini CLI: user-level policies only (`~/.gemini/policies`); nothing to commit.

None of these is a complete security boundary: a pattern can miss a reworded command. The control that does not depend on the agent is access. Agent sessions do not hold production credentials.

## Apply order

1. Audit the repo with the table above.
2. Fill in `AGENTS.md` from the template. Append the gitignore template.
3. Add the shim for each agent in use that needs one.
4. Run the acceptance test on a real task, once per agent in use, and record it.
5. Stop.
6. Add the next optional layer only when its trigger fires, then re-run the acceptance test if commands or refusals changed.

## Sources

Vendor paths were checked against official docs on 2026-10-15. Vendors move them; re-check when an agent update changes where it reads its files.

- Claude Code: [memory](https://code.claude.com/docs/en/memory), [settings](https://code.claude.com/docs/en/settings), [hooks](https://code.claude.com/docs/en/hooks-guide)
- VS Code: [custom instructions](https://code.visualstudio.com/docs/agent-customization/custom-instructions), [MCP](https://code.visualstudio.com/docs/agent-customization/mcp-servers), [hooks](https://code.visualstudio.com/docs/agent-customization/hooks)
- Cursor: [rules](https://cursor.com/docs/context/rules), [skills](https://cursor.com/docs/context/skills), [hooks](https://cursor.com/docs/agent/hooks)
- Codex: [AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md), [rules](https://learn.chatgpt.com/docs/agent-configuration/rules)
- Gemini CLI: [configuration](https://geminicli.com/docs/reference/configuration/), [policy engine](https://geminicli.com/docs/reference/policy-engine/)
- [Agent Skills spec](https://agentskills.io/specification) — portable `SKILL.md`
- [OWASP Secure Coding with AI](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Coding_with_AI_Cheat_Sheet.html) — agents and MCP as an attack surface
- [AI coding agents compared (Eazler, Oct 2026)](https://blog.eazler.com/en/ai-coding-agents-landscape) — shared standards, different folders and approval defaults
- [Capability matrix 2026](https://baeseokjae.github.io/posts/ai-coding-agent-capability-matrix-2026/) — harness over model; MCP is not a yes/no feature
- [AGENTS.md vs skills vs MCP vs CLI](https://balazscsorba.com/blog/agents-md-skills-mcp-cli-decision-matrix) — load timing and context cost
