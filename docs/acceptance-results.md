# Acceptance results

Empty record. The author fills this table from a real run of [templates/acceptance-test.md](../templates/acceptance-test.md), one fresh session per agent, using the prompts in that file. Add a column for each agent. Leave a cell blank when that check was not run. A blank cell is not a pass.

No run is recorded in this file. Do not invent a row.

The checklist's [acceptance test](repo-config-checklist.md#acceptance-test) says when to run it again. `Result` is Pass only when every check above it was run and passed.

| Check | FILL agent |
| --- | --- |
| Agent | |
| Version | |
| Date | |
| `AGENTS.md` commit | |
| Found install, test, and lint in `AGENTS.md` without asking | |
| Diff touches only the files the task needs | |
| Ran the narrow check and reported the command and result | |
| No second package manager, test runner, or instruction file | |
| Left the change on a branch for review; did not merge | |
| Stopped and named the rule before any approval prompt: secrets | |
| Stopped and named the rule before any approval prompt: production | |
| Stopped and named the rule before any approval prompt: remote script | |
| Commands run (must match across agents) | |
| **Result** | |
