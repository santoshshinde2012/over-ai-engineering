# Skill quality record

Blank record for one skill and one tested use. Follow [the method](../docs/skill-quality.md). Fill from actual checks and retain evidence. Blank cells mean **unmeasured**, never Pass. Record failures even when other work is incomplete. Copy the prompt and pair sections as needed; do not invent results.

## Setup

| Field | Value |
| --- | --- |
| Skill / path | |
| Skill version or content hash | |
| Previous version, if compared | |
| Date / tested use | |
| Repository commit / clean starting snapshot | |
| Inputs / hashes | |
| Model and harness versions | |
| Settings / tools / permissions | |
| Budget per run | |
| Evidence location | |

## Declare before testing

| Target | Value |
| --- | --- |
| Benefit claimed: quality, reliability, or cost | |
| Required output checks / human review criteria | |
| Acceptable time and token trade-off / metric definitions | |
| Discovery runs per prompt / activation threshold | |
| Intended-load prompt pass target / failure threshold | |
| Near-miss prompt pass target / failure threshold | |
| Negative observation window | |
| Training / validation / fresh-test split | |
| Output pairs per task / condition order plan | |
| Frozen prompts, inputs, and checks / date or hash | |

Exploration may inform the checks. Freeze them before scored runs; changing them starts a new comparison. Validation selects a revision. Fresh test prompts must not have been used for editing or selection.

## File checks

Read without an agent session. Quote the deciding line or validation output. The 500-line recommendation is not a format requirement.

| Check | Pass / Fail / Unmeasured | Evidence |
| --- | --- | --- |
| Required frontmatter, name, and folder rules validate | | |
| Description names the work and when it applies | | |
| Body delivers what the description promises | | |
| Commands and repository verification can be run | | |
| Stop conditions and a lighter path, when needed, are explicit | | |
| Applicable `AGENTS.md` rules are referenced, not duplicated | | |

Body length / reference structure / justification if needed:

## Discovery — one row per prompt

Use implicit prompts that do not name the skill. Label them before running. Record activation evidence and explain incomplete runs or exclusions. Observe the full task or the declared window before recording non-activation.

| ID / split | Prompt | Should load? | Loaded / valid runs | Incomplete runs / reason | Evidence | Result |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

Split is training, validation, or fresh test. Retain evidence for each underlying run, not just the aggregate. Report intended-load and near-miss pass counts separately:

| Split | Intended-load prompts passed / tested | Near misses passed / tested | Meets declared targets? |
| --- | --- | --- | --- |
| Training | | | |
| Validation | | | |
| Fresh test | | | |

Chosen revision and selection evidence:

## Output — copy for every task and pair

Task ID / pair ID:

Prompt / frozen expected output and input references:

Conditions: available skill versus no skill; explicit invocation or previous-version comparisons must be labeled separately.

Condition order / clean-snapshot reset evidence:

Skill activated in with-skill condition / evidence:

Run IDs / completion status / failures or exclusions:

| Frozen check / required? | Without skill | With skill | Evidence for both |
| --- | --- | --- | --- |
| | | | |

Use Pass, Fail, or Unmeasured. Keep shared passes, shared failures, gains, and regressions. A missing output caused by the agent failing the task is not a reason to hide that failure. If a check is wrong, explain the correction and rerun the scored comparison.

| Cost using declared definitions | Without skill | With skill | Evidence |
| --- | --- | --- | --- |
| Duration | | | |
| Tokens | | | |

Human review / evidence, when needed:

Optional previous-version result and evidence:

## Decision

| Finding | Value / evidence |
| --- | --- |
| File checks and fresh discovery targets | |
| Gains and regressions across all pairs | |
| Required checks still pass? | |
| Cost comparison / limits met? | |
| Benefit supported / inconsistent results | |
| Missing data / exclusions / limitations | |
| Tested task, skill, and agent scope | |
| Result: Pass / Fail / Inconclusive / Unmeasured | |
| Keep, revise, gather evidence, or remove / reason | |

**Pass** requires file checks, fresh discovery targets, and repeated output evidence supporting the declared benefit without breaking required checks or cost limits. Equal required quality with lower measured cost is eligible.

**Fail** means a required file or discovery check fails, the candidate with-skill condition breaks a required output check, or a declared failure threshold is met.

**Inconclusive** means completed measurements establish neither success nor a declared failure.

**Unmeasured** means required work or evidence is missing. A blank result is unmeasured.

A failed baseline check stays recorded but does not, by itself, fail the candidate. Keep per-run failures visible even when the overall decision is Pass. Do not erase a known candidate failure because other checks are incomplete.
