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
%%{init: {'theme': 'base', 'themeCSS': '.flowchart-link[style*=stroke-width] { marker-end: none !important; }', 'flowchart': {'htmlLabels': false, 'curve': 'step', 'padding': 18, 'nodeSpacing': 28, 'rankSpacing': 40}, 'themeVariables': {'fontFamily': 'arial', 'fontSize': '15px', 'lineColor': '#5C6B7A', 'textColor': '#1C2833', 'primaryColor': '#F7F8FA', 'primaryTextColor': '#1C2833', 'primaryBorderColor': '#1B3A4B', 'clusterBkg': '#F7F8FA', 'clusterBorder': '#D5DCE3', 'edgeLabelBackground': '#FFFFFF'}}}%%
flowchart TB
  accTitle: One source of truth, thin shims
  accDescr: Cursor, Codex, and VS Code read AGENTS.md directly. Claude Code reads it through CLAUDE.md, and Gemini CLI through .gemini/settings.json. Navy is the source file. Blue is a shim.

  agents("AGENTS.md")

  subgraph direct["Read directly"]
    direction LR
    cursor("Cursor")
    codex("Codex")
    copilot("VS Code / Copilot")
    cursor ~~~ codex
    codex ~~~ copilot
  end

  subgraph via["Read through a shim"]
    direction LR
    claudeShim("CLAUDE.md") --> claude("Claude Code")
    geminiShim(".gemini/settings.json") --> gemini("Gemini CLI")
  end

  agents --> direct
  direct --> via

  class agents source
  class claudeShim,geminiShim shim
  class cursor,codex,copilot,claude,gemini agent

  classDef source fill:#1B3A4B,stroke:#1B3A4B,color:#FFFFFF
  classDef shim fill:#E6EEF5,stroke:#1B3A4B,color:#1B3A4B
  classDef agent fill:#F7F8FA,stroke:#5C6B7A,color:#1C2833

  style direct fill:#F7F8FA,stroke:#D5DCE3,color:#1B3A4B
  style via fill:#F7F8FA,stroke:#D5DCE3,color:#1B3A4B
  linkStyle 5 stroke-width:0px,fill:none
```

Navy is the source of truth. Blue is a shim. Gray is an agent.

## How to use this repo

Audit the product repo, add the mandatory files, and run the acceptance test on every agent in use. Fix and re-test until it passes, then stop. Add an optional layer only when its trigger fires, and test again. The checklist's [apply order](docs/repo-config-checklist.md#apply-order) is this flow.

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'htmlLabels': false, 'curve': 'basis', 'padding': 18, 'nodeSpacing': 48, 'rankSpacing': 48}, 'themeVariables': {'fontFamily': 'arial', 'fontSize': '15px', 'lineColor': '#5C6B7A', 'textColor': '#1C2833', 'primaryColor': '#F7F8FA', 'primaryTextColor': '#1C2833', 'primaryBorderColor': '#1B3A4B', 'edgeLabelBackground': '#FFFFFF'}}}%%
flowchart TD
  accTitle: How to apply the checklist
  accDescr: Audit, write AGENTS.md and the gitignore, add shims, then test on every agent. A no result fixes AGENTS.md or the shim and tests again. A yes result stops. A dashed line adds one optional layer only after a trigger fires.

  audit("Audit the repo") --> setup("AGENTS.md and gitignore")
  setup --> shims("Add the shims")
  shims --> test{"Passes on every agent?"}
  test -->|Yes| stop(["Stop"])
  stop -.->|A trigger fires| layer("Add one optional layer")
  test -->|No| fix("Fix AGENTS.md or the shim")
  fix -->|Re-test| test

  class audit,setup,shims step
  class test decision
  class fix rework
  class stop terminal
  class layer later

  classDef step fill:#F7F8FA,stroke:#1B3A4B,color:#1C2833
  classDef decision fill:#F4F0E6,stroke:#6F6248,color:#1C2833
  classDef rework fill:#F6F0ED,stroke:#7A4E45,color:#1C2833
  classDef terminal fill:#1B3A4B,stroke:#1B3A4B,color:#FFFFFF
  classDef later fill:#E6EEF5,stroke:#1B3A4B,color:#1B3A4B
```

Solid lines are the required path. Stone is the decision. Clay is rework. The dashed line runs only after a trigger fires, and that layer is tested again.

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
