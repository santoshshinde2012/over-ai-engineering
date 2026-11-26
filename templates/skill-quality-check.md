# Skill quality record

Empty record for one skill. Fill it from real runs, using [docs/skill-quality.md](../docs/skill-quality.md). Add a column if you compare agents. Leave a cell blank when that check was not run. A blank cell is not a pass. Do not invent a row.

Skill:
Path:
Date:
Agent and version:
`SKILL.md` commit:

## Static checks

No agent session. Quote the line that decides the row.

| Check | Pass / Fail | Evidence |
| --- | --- | --- |
| `name` is 1 to 64 characters, lowercase letters, numbers, and hyphens, with no leading, trailing, or doubled hyphen, and it matches the folder | | |
| `description` is 1 to 1024 characters and says what the skill does and when to use it | | |
| The steps can do what the description promises | | |
| The steps name the verify command from `AGENTS.md` | | |
| The file says when to stop, including a refusal in `AGENTS.md` | | |
| The body is under 500 lines and does not paste `AGENTS.md` again | | |

## Trigger checks

Write these prompts before you edit the description to match them. Mark which prompts you held out. An unrelated prompt does not count as a negative. A should-load prompt passes when the skill loads on more than half the runs. A should-not prompt passes when it loads on fewer than half. Half is the default in the description guide, not a measured law.

| ID | Held out | Should load | Prompt | Runs | Times loaded | Rate | Pass / Fail | How you saw the load |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| | yes / no | yes / no | | | | | | |

Training pass count:
Held-out pass count:

## Output checks

One fresh session with the skill, and one without it, per prompt. Quote the output or the command log.

| Prompt | Assertion | With skill | Without skill |
| --- | --- | --- | --- |
| | | Pass / Fail: | Pass / Fail: |

Pass count with skill:
Pass count without skill:
Tokens and duration, if the client reported them:

## Result

Pass only when every static check was run and passed, the held-out trigger prompts were run, and the with-skill pass count is higher on the assertions the no-skill run failed. You also accept the extra tokens and time. Otherwise the result is Fail, or blank if you did not run the checks.

Result:
