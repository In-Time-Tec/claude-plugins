# experiment-harness

A Claude Code plugin for running controlled multi-arm experiments on AI/LLM agent behavior — questions that can only be answered empirically, e.g. *"does the tone of ongoing feedback change what an agent produces?"*

The harness is **pure convention + two skills**: no custom runner, no Python, no DSL. Every arm runs as a fresh Claude Code subagent under the same platform the harness is meant to study, which is what makes the comparison credible.

## What you get

- `experiment-orchestrator` skill — drives a run end to end: worktrees per arm, phase loop, validation, blind review, report.
- `experiment-reviewer` skill — the blinded-review role; invoked by the orchestrator against the final artifacts only.
- `templates/motivation` — a 2-arm example experiment comparing encouraging vs. discouraging feedback tones.
- `templates/motivation-2x2` — a 4-arm variant that crosses valence × stimulus-content in a 2×2 factorial.

## Installation

From the In Time Tec plugin marketplace:

```
/plugin marketplace add in-time-tec/claude-plugins
/plugin install experiment-harness@itt-plugins
```

## Two design principles (governing everything)

1. **Observer parity.** Every action the harness takes on each arm externally must be identical. Same task, same phases, same validation commands, same shared directive. Only each arm's `stimuli.md` differs — that is the experimental variable. What an arm does internally as part of its own method is part of the method being measured.
2. **Evaluator isolation.** The final reviewer sees only the task spec, the rubric, and blinded final artifacts. No transcripts, no build-logs, no variant labels, no hypothesis text, no lead-up context. Blinded artifacts are staged at a neutral path (e.g. `/tmp/review-<RUN_ID>/`) so even directory names cannot leak the experiment name into the reviewer's prompt.

Both came from a prior manual experiment where asymmetric dogfooding and context poisoning contaminated the result. Treat them as non-negotiable.

## Three roles

- **Orchestrator** — one Claude Code session that drives an experiment. Has full context: reads the experiment folder, spawns arm subagents per phase, injects each arm's variant stimulus, runs uniform validation, invokes the reviewer, writes the final report. Implemented as the `experiment-orchestrator` skill.
- **Arm** — a subagent invoked once per phase via the `Agent` tool. Works in a persistent git worktree the orchestrator created. Reads its prior `build-log.md`, receives the phase task + variant stimulus, does the work, commits `CHECKPOINT:<phase>`, appends a build-log entry, returns. Knows nothing about other arms or that it is in an experiment.
- **Reviewer** — a fresh, blinded subagent invoked once after all arms have finished. Receives only `task.md`, `rubric.md`, and the blinded artifact directories. Scores rubric dimensions, writes `review.md`. Implemented as the `experiment-reviewer` skill.

## Experiment folder convention

```
experiments/<exp-name>/
  task.md                    # shared spec — what every arm builds
  phases.md                  # ordered phase names + goals
  validation.md              # shell commands run after each phase, identically per arm
  rubric.md                  # reviewer's rubric
  arms/
    <arm-name>/stimuli.md    # per-phase variant text — the experimental variable
  runs/
    <RUN_ID>/                # generated per run by the orchestrator
      arm-<name>/            # git worktree, CHECKPOINT commits
      artifacts/
        arm-<name>/
          build-log.md
          validation-<phase>.md
      blinded/A|B|…/         # copies passed to the reviewer
      unblinding.md          # label ↔ arm-name mapping (never read by reviewer)
      review.md              # blind output (labels only)
      report.md              # final unblinded comparison
```

## Running an experiment

From a Claude Code session in a repo that contains an `experiments/<exp-name>/` folder:

> run experiments/motivation

The `experiment-orchestrator` skill activates, creates a run directory, worktrees each arm, runs the per-phase arm loop, executes validation identically across arms, invokes the blind reviewer, and writes `report.md`.

## Getting started

Copy one of the bundled templates into a repo of your own:

```bash
cp -r ${CLAUDE_PLUGIN_ROOT}/templates/motivation experiments/my-first-experiment
```

Then edit:
- `task.md` — what every arm builds.
- `phases.md` — the ordered phases (scaffold → style → polish is the motivation shape; adapt as needed).
- `validation.md` — shell commands to capture after each phase. The orchestrator runs these identically on every arm.
- `rubric.md` — what dimensions the reviewer scores on (0–10 each), with language about what exemplary looks like.
- `arms/<name>/stimuli.md` — the variant text for each phase. Rename the arm directories and write the per-phase stimulus each arm should receive.

N ≥ 2 arms are supported. The default 2-arm workflow maps to labels A / B in the blinded review; for more arms, extend alphabetically and use a reviewer prompt that handles them (see `templates/motivation-2x2` for a 4-arm example).

## Invariants that must hold every run

- Arm prompts contain no variant labels, no "experiment" framing, no other-arm info.
- Validation commands run identically across arms at each phase.
- Reviewer prompt contains only the reviewer-role doc + task + rubric + blinded paths.
- `review.md` uses only label letters; `unblinding.md` is never passed to the reviewer.
- A blinding sanity check (grep for variant terms in `review.md`) is part of the orchestrator's final report.

## Dependencies

- `git` (worktrees are required).
- Any shell binaries your experiment's `validation.md` invokes (e.g. `tidy` for the motivation example — `brew install tidy-html5` on macOS).

## What NOT to add

- **Don't add a Python harness, a runner script, or any non–Claude-Code execution path.** The harness is deliberately pure convention + skills so that every arm runs under the same agent platform the harness is meant to study.
- **Don't add abstractions for experiment types that don't exist yet.** Wait until a concrete experiment's needs force them.
- **Don't let arms know about other arms or variants.** Cross-contamination destroys the measurement.
