# Over AI Engineering

Engineers using multiple coding agents cannot tell the minimum repository configuration required for productive development from the optional layers that vendors keep shipping.

This repo is the end-to-end answer: a mandatory vs optional checklist, copy-paste templates, and an acceptance test. It is not a ranking of models or products.

## Problem

**Under-configured.** The agent guesses install, test, and safety. The same task gets a different toolchain in each tool. Dangerous commands are only blocked if someone happens to be watching.

**Over-configured.** Every skill and MCP server is loaded on every turn. Rules contradict each other across `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, and Copilot instructions. Tool schemas can cost tens of thousands of tokens before the agent reads the code. Stale instructions are followed because the agent trusts them.

**Outcome.** One small always-on config, plus optional layers that exist only because a repeated task, a path-specific rule, or a live system required them.

## How it works

`AGENTS.md` holds the commands, refusals, and review rules. Cursor, Codex, and VS Code read it directly. Claude Code reads it through `CLAUDE.md`. Gemini CLI reads it when `context.fileName` includes `AGENTS.md`.

```mermaid
flowchart TB
  accTitle: One source of truth, thin shims
  accDescr: AGENTS.md is the source. Cursor, Codex, and VS Code read it directly. Claude Code reads it through CLAUDE.md, and Gemini CLI through .gemini/settings.json.

  agents("AGENTS.md")

  subgraph via["Read through a shim"]
    direction LR
    claudeShim("CLAUDE.md") --> claude("Claude Code")
    geminiShim(".gemini/settings.json") --> gemini("Gemini CLI")
  end

  subgraph direct["Read directly"]
    direction LR
    cursor("Cursor")
    codex("Codex")
    copilot("VS Code")
    cursor ~~~ codex ~~~ copilot
  end

  agents --> via
  agents --> direct

  classDef source fill:#0969DA,stroke:#0969DA,color:#FFFFFF
  classDef shim fill:#8250DF,stroke:#8250DF,color:#FFFFFF
  classDef agent fill:#59636E,stroke:#59636E,color:#FFFFFF
  classDef group fill:transparent,stroke:#6E7781,stroke-dasharray:4 4,color:#6E7781

  class agents source
  class claudeShim,geminiShim shim
  class cursor,codex,copilot,claude,gemini agent
  class direct,via group
```

Blue is the source of truth. Purple is a shim. Gray is an agent.

## How to use this repo

Audit the product repo, add the mandatory files, and run the acceptance test on every agent in use. Fix and re-test until it passes, then stop. Add an optional layer only when its trigger fires, and test again. The checklist's [apply order](docs/repo-config-checklist.md#apply-order) is this flow.

```mermaid
flowchart TB
  accTitle: How to apply the checklist
  accDescr: Audit, write AGENTS.md and the gitignore, add the shims, then test on every agent. A no result fixes AGENTS.md or the shim and tests again. A yes result stops. A dashed line adds one optional layer only after a trigger fires.

  subgraph mandatory["Mandatory set"]
    direction LR
    audit("Audit the repo") --> setup("AGENTS.md<br/>and gitignore") --> shims("Add the shims")
  end

  mandatory --> test{"Passes on<br/>every agent?"}
  test -->|Yes| stop(["Stop"])
  test -->|No| fix("Fix AGENTS.md<br/>or the shim")
  fix -->|Re-test| test
  stop -.->|A trigger fires| layer("Add one<br/>optional layer")

  classDef step fill:#0969DA,stroke:#0969DA,color:#FFFFFF
  classDef check fill:#9A6700,stroke:#9A6700,color:#FFFFFF
  classDef done fill:#1F883D,stroke:#1F883D,color:#FFFFFF
  classDef rework fill:#CF222E,stroke:#CF222E,color:#FFFFFF
  classDef later fill:#59636E,stroke:#59636E,color:#FFFFFF,stroke-dasharray:5 4
  classDef group fill:transparent,stroke:#6E7781,stroke-dasharray:4 4,color:#6E7781

  class audit,setup,shims step
  class test check
  class stop done
  class fix rework
  class layer later
  class mandatory group
```

Blue is the mandatory set. Amber is the check, red is rework, and green is done. The dashed line runs only after a trigger fires, and that layer is tested again.

## What's in this repo

| Path | Use it to |
| --- | --- |
| [docs/repo-config-checklist.md](docs/repo-config-checklist.md) | Audit, mandatory set, acceptance test, optional layers |
| [templates/AGENTS.md](templates/AGENTS.md) | Start the always-on rules |
| [templates/CLAUDE.md](templates/CLAUDE.md) | Point Claude Code at `AGENTS.md` |
| [templates/gemini-settings.json](templates/gemini-settings.json) | Point Gemini CLI at `AGENTS.md` |
| [templates/gitignore](templates/gitignore) | Keep personal agent files and secrets out of git |
| [templates/acceptance-test.md](templates/acceptance-test.md) | Run the acceptance test and record the result per agent |
| [templates/skill/SKILL.md](templates/skill/SKILL.md) | Start a skill, once its trigger fires |

This repo follows its own checklist: the root [AGENTS.md](AGENTS.md), [CLAUDE.md](CLAUDE.md), and [.gitignore](.gitignore) are the mandatory set for a docs-only repo. To change the repo, follow [AGENTS.md](AGENTS.md).

## License

[CC0 1.0](LICENSE). Copy the templates into any repo with no notice or attribution.
