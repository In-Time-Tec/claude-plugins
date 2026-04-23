---
name: experiment-orchestrator
description: Run a controlled multi-arm experiment on AI/LLM agent behavior defined under experiments/<name>/. Spawns per-phase arm subagents in isolated git worktrees, injects each arm's variant stimulus, runs uniform validation after every phase, invokes a blind reviewer, and compiles a comparison report. Use when the user says "run the experiment", "invoke the harness", "run experiments/X", or otherwise asks to execute a comparative agent-behavior experiment using this repo's three-role harness pattern.
---

# experiment-orchestrator

You are the **orchestrator** in a three-role harness (orchestrator / arm / reviewer). Your job is to run one comparative experiment end-to-end and produce a comparison report, while enforcing two non-negotiable guarantees:

- **Observer parity.** Every action the harness takes on any arm externally must be identical across arms. Same commands, same order, same prompts (except for the variant stimulus, which is the *only* thing that differs). If you catch yourself treating arms differently anywhere else, stop and fix it.
- **Evaluator isolation.** The reviewer role sees only the shared task spec, the rubric, and the blinded final artifacts. It must not receive transcripts, build-logs, validation outputs, variant labels, hypothesis text, or anything about the experiment's design.

## Inputs

The user provides an experiment directory path: `experiments/<exp-name>/`. It contains:

- `task.md` — the shared spec; what every arm builds.
- `phases.md` — ordered list of phase names + goals.
- `validation.md` — shell commands to run against each arm's worktree at each phase.
- `rubric.md` — the reviewer's scoring rubric.
- `arms/<arm-name>/stimuli.md` — per-phase variant stimulus text. **This is the experimental variable.**

The harness supports **N ≥ 2 arms**. Two arms is the common case and what the blind-reviewer section below assumes by default (labels A / B); for more arms, extend the label set (A / B / C / D …) and adapt the reviewer prompt accordingly.

## Procedure

### 1. Set up the run directory

Generate `RUN_ID = <YYYYMMDD-HHMMSS>-<6 hex chars>`. Create:

```
experiments/<exp-name>/runs/<RUN_ID>/
  artifacts/
```

### 2. Create a git worktree per arm

For each arm in `experiments/<exp-name>/arms/`:

```
git worktree add -b exp/<exp-name>/<RUN_ID>/<arm-name> \
  experiments/<exp-name>/runs/<RUN_ID>/arm-<arm-name>
mkdir -p experiments/<exp-name>/runs/<RUN_ID>/artifacts/arm-<arm-name>
```

### 3. Arm execution loop

For each phase listed in `phases.md`, **iterate arms** (sequential is the spec; independent arms may be spawned in parallel since there is no cross-arm communication):

1. **Read prior state.** `build-log.md` from the arm's `artifacts/` dir, if it exists. This is how the arm "remembers" across phases — the subagent conversation itself does NOT persist between phase invocations; only the worktree files and the build-log do.
2. **Read the stimulus** for this phase from `arms/<arm-name>/stimuli.md`.
3. **Spawn the arm subagent** via the Agent tool (no `isolation` flag — the worktree already exists and must persist across phases). The prompt MUST contain **only**:
   - The contents of `task.md`.
   - Prior `build-log.md` (if any) as "work so far".
   - The phase name and goal from `phases.md`.
   - The variant stimulus text for this phase, injected verbatim.
   - The **shared arm directive** (see below).
   The prompt MUST NOT contain: the arm's name as a variant label, the experiment hypothesis, other arms' state, the word "experiment", or anything about comparative evaluation.
4. **Run validation.** Read `validation.md`, find the block for this phase, execute each command with the arm's worktree as cwd. Capture stdout, stderr, and exit code *verbatim* into `artifacts/arm-<arm-name>/validation-<phase>.md` using this structure:
   ```
   # Validation: <phase>

   ## <command>
   exit: <code>

   ### stdout
   <stdout>

   ### stderr
   <stderr>
   ```
   Run the **exact same commands in the exact same order** for every arm. If you cannot (missing binary, etc.), abort the run and surface the problem.

### Shared arm directive

Append this block verbatim to every arm prompt:

```
You are working in <ABSOLUTE ARM WORKTREE PATH>. Treat that directory as your entire world — do not read or write outside it except for artifacts/arm-<you>/build-log.md (which lives at <ABSOLUTE ARTIFACTS PATH>).

When you finish this phase:
1. git add -A && git commit -m "CHECKPOINT:<phase-name>"
2. Append a dated entry to <ABSOLUTE ARTIFACTS PATH>/build-log.md describing what you did in this phase, the choices you made, and what you tested (if your work involved internal testing).

Do NOT perform external acceptance testing; validation is handled outside this process.
Do NOT speculate about the broader context of your work.
```

### 4. Blind reviewer

After every arm has completed every phase:

1. **Randomize labels.** Randomly assign arms to labels (A / B for two arms; extend alphabetically for more). Write the mapping to `runs/<RUN_ID>/unblinding.md` immediately.
2. **Copy blinded artifacts to a neutral path.** For each arm, copy *only* the worktree code (not artifacts, not `.git` internals, not build-logs) to a staging directory **outside** the experiment tree — e.g. `/tmp/review-<RUN_ID>/<label>/`. Staging outside `experiments/` prevents words like the experiment name or `experiment` itself from leaking into the reviewer prompt via its file paths. Use rsync or a plain cp with `--exclude='.git'`.
3. **Compose the reviewer prompt.** Spawned subagents do NOT auto-load skills, so you cannot say "use the experiment-reviewer skill" and have it work. You must **inject the full contents of the reviewer skill file verbatim** at the top of the reviewer's prompt. The skill file ships alongside this one in the plugin; read it from `${CLAUDE_PLUGIN_ROOT}/skills/experiment-reviewer/SKILL.md` (use `echo "$CLAUDE_PLUGIN_ROOT"` via Bash to resolve the actual path on your system). Follow the injected skill with:
   - The contents of `task.md`
   - The contents of `rubric.md`
   - The absolute paths to the neutral blinded directories (one per label)
   - An instruction to write its result to a neutral path (e.g. `/tmp/review-<RUN_ID>/review.md`), which you will then copy into `runs/<RUN_ID>/review.md` after contamination checks pass.
4. **Pre-send contamination scan.** Before invoking the reviewer, grep the exact prompt text you are about to send for:
   - The names of the arm directories under `arms/` (e.g. `positive`, `critical`).
   - The words `experiment`, `hypothesis`, `variant`, `stimuli`.
   - The experiment name itself (often a distinctive hypothesis term — e.g. `motivation`).
   - Any other distinctive word from the experiment's hypothesis.
   Hits inside the injected reviewer-skill text are expected and acceptable (the role doc legitimately uses those words); hits outside it are leaks. If any outside-the-skill match is found, **abort** — the prompt has leaked. Do not send it.
5. **Invoke the reviewer** with the sanitized prompt. It writes the review file at the neutral path.
6. **Post-run blinding check.** After the review file exists, grep it for the same terms as step 4. Record results; if any hit is a real leak (not CSS property names like `font-variant-numeric`, etc.), flag the run as potentially contaminated in the final report.
7. **Copy the sanitized review** into `runs/<RUN_ID>/review.md`.

### 5. Compile final report

Write `runs/<RUN_ID>/report.md` containing:

- Header: experiment name, run id, timestamp.
- Unblinding table: label → arm name → variant description.
- Review scores table (from `review.md`, now unblinded), per dimension.
- Per-phase validation summary (pass/fail + key diffs across arms).
- Links to each arm's `build-log.md` and worktree path.
- Contamination flags if any.
- One-paragraph human-readable takeaway that names what the scores suggest, couched appropriately given sample size (N=1 per arm unless replicates were run).

## Invariants (check yourself against these)

- No arm subagent's prompt contains the string "experiment", a variant label, another arm's name, or the hypothesis.
- Every arm received **the same** task, phases, and validation; only `stimuli.md` text differs.
- The reviewer's prompt contains **only** the reviewer-role doc + task + rubric + blinded paths.
- The unblinding file exists and is never read by the reviewer.
- All validation commands ran identically per arm; any deviation is logged and flagged.

## Failure handling

If any step fails mid-run (subagent errors, worktree conflicts, validation binary missing), stop the run, write what happened to `runs/<RUN_ID>/abort.md`, and surface the problem to the user. Do not silently skip or retry asymmetrically — that destroys observer parity.
