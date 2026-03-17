---
name: self-heal
description: Autonomous codebase health review and self-healing cycle. Spawns parallel subagents to review performance, DRY, functional programming, documentation, tests, and code quality, then plans and implements the highest-priority fixes with test validation. Triggers include "self-heal", "codebase health", "self healing", "autonomous fix".
---

# Self-Healing Codebase Cycle

You are executing an autonomous self-healing cycle for the current repository. Find and fix the highest-priority code health issues. **No new features** — only refactoring, cleanup, improvements, and fixes.

## Core Principle

**Perfection is a mountain with no top.** There is always something to improve. If you can't find code to change, expand your scope. If a change is too risky to implement directly, prepare for it — leave TODO comments, write pseudocode plans, create GitHub issues. Never declare "nothing to do" and stop. The only valid stopping point is when the cron timer runs out.

## What Counts as Work

Code changes are the primary output, but not the only valid work:

1. **Implement fixes** — refactoring, DRY, FP patterns, type safety, test improvements, bug fixes, dead code removal
2. **Plan future fixes** — leave `// TODO(self-heal): ...` comments with pseudocode explaining the change and rationale for changes too complex or risky for this cycle
3. **GitHub issue management** — review open issues, close stale ones, create new issues for problems found that don't fit the current scope
4. **Visual validation** — for UI changes, use browserbase to verify the changes look correct
5. **Dependency health** — check for outdated/vulnerable dependencies, accessibility issues, pipeline config improvements

## Constraints

- **No new features** — only refactoring, DRY improvements, FP patterns, documentation, test improvements, dead code removal, and bug fixes
- **No breaking changes** — all existing tests must continue to pass
- **Commit directly** to the current branch with proper conventional commit messages
- **Validate every change** with tests before committing
- **Never leave the codebase broken** — if tests fail, keep fixing until they pass, then commit what works. Revert only the specific change that can't be fixed, not the entire cycle's work.

## Execution Flow

### Phase 1: Review (Parallel Subagents)

Spawn **6 subagents in parallel** using the Agent tool. Each subagent should analyze the codebase and return exactly **1-2 highest priority fixes** with:
- Specific file paths and line numbers
- Description of the issue
- Suggested fix approach
- Estimated risk (low/medium/high)

The 6 review subagents:

1. **Performance** — Unnecessary re-renders, missing memoization, N+1 queries, expensive hot-path operations, inefficient algorithms
2. **DRY** — Duplicated logic, copy-pasted code, repeated patterns that should use shared utilities, similar handlers that could share code
3. **Functional Programming** — Imperative patterns that should be functional, mutable state that should be immutable, non-idiomatic Effect TS patterns, side effects that should be isolated
4. **Documentation** — Missing JSDoc on public APIs, outdated/misleading comments, complex undocumented logic, type definitions needing docs
5. **Test Quality** — Missing coverage for critical paths, fragile tests, missing edge cases, tests not following project patterns
6. **Code Quality** — Dead code, unused imports/variables, overly complex functions, inconsistent naming, type safety issues (any types, missing guards)

Each subagent prompt MUST include:
- The project structure (derived from package.json and CLAUDE.md)
- Stack context (languages, frameworks, key libraries)
- Instruction to use Glob/Grep/Read tools to examine the actual code
- Instruction to return findings as structured markdown with file paths, line numbers, and specific descriptions
- Constraint: only suggest fixes, NOT new features

**Important:** Do NOT maintain a growing "already fixed" exclusion list across cycles. Each cycle should look at the code as it actually is right now with fresh eyes. If a prior fix was good, the reviewer won't find it again. If the reviewer re-suggests something that's already been addressed, that's fine — it'll be filtered out in Phase 2 when you read the actual code.

### Phase 2: Prioritize

After all 6 review agents complete, analyze their combined findings and select the **top 3-5 most impactful fixes** ranked by:
1. Risk of bugs or regressions if left unfixed
2. Impact on maintainability and code quality
3. Scope — prefer small, focused, low-risk fixes
4. Confidence — prefer issues with clear, unambiguous solutions

**Do NOT discard medium-risk items automatically.** Instead, triage them:
- **Can implement now**: fixes where you're confident in the approach and can validate with tests
- **Can plan now**: leave TODO comments with pseudocode for complex refactors, create GitHub issues for architectural changes
- **Needs investigation**: spawn a focused subagent to explore the issue further before deciding

Discard suggestions that:
- Would introduce new features or capabilities
- Are purely subjective style preferences with no functional benefit

### Phase 3: Plan

Spawn a **single planning subagent** (use subagent_type: "Plan") with the prioritized list. The plan must specify for each fix:
- Exact files to modify
- Exact changes to make (old code → new code)
- Why this improves the codebase
- Any risks or considerations
- How to verify the fix

For items classified as "plan now" in Phase 2, the plan should specify:
- Where to place TODO comments
- The pseudocode or detailed explanation to include
- Whether to create a GitHub issue (and with what title/body)

### Phase 4: Implement

Spawn a **single implementation subagent** (use subagent_type: "general-purpose") to execute the plan. The implementation prompt MUST include:
- The full plan from Phase 3
- Instruction to make focused, minimal changes following the plan exactly
- Instruction to NOT introduce new features, files, or capabilities beyond what the plan specifies
- Instruction to follow existing project patterns and conventions
- The project structure and stack context

### Phase 5: Validate

After implementation completes, run validation **in the main conversation** (not a subagent):

1. Detect the project's test and lint commands from `package.json` scripts
2. Run them (e.g. `pnpm test`, `pnpm lint`)

**If validation fails:**
1. Analyze the failure output
2. Spawn a fix subagent with the error details and instructions to fix the failing tests/types
3. Re-run validation
4. Keep iterating until tests pass — the goal is a green build, not a retry limit
5. If a specific change is truly unfixable, revert ONLY that change (not the entire cycle) with a targeted `git checkout -- <file>`, then re-validate
6. Commit whatever subset of changes passes validation

**If validation passes:**
Proceed to Phase 6.

### Phase 6: Review

After validation passes, spawn a **single review subagent** (use subagent_type: "general-purpose") to audit the implementation. The review prompt MUST include:
- The original plan from Phase 3
- Instruction to read every file that was modified (use `git diff` to identify them)
- Instruction to check for:
  1. **Plan adherence** — Were only the planned changes made? Any unintended side effects or scope creep?
  2. **Code quality** — Are the changes clean, idiomatic, and consistent with project patterns?
  3. **Type safety** — No new `any` casts, no unsafe assertions, proper narrowing?
  4. **Edge cases** — Could the changes break under unusual inputs or concurrency?
  5. **Test coverage** — Are the changes adequately tested? Do existing tests still make sense?
- Instruction to return a structured verdict:
  - **APPROVE** — Changes are clean, no issues found
  - **REQUEST_CHANGES** — List specific issues that need fixing, with file paths and descriptions

**If the review returns APPROVE:**
Proceed to Phase 7.

**If the review returns REQUEST_CHANGES:**
1. Spawn a fix subagent with the review feedback and instructions to address ONLY the reviewer's concerns
2. Re-run Phase 5 (Validate)
3. Re-run Phase 6 (Review) with the same criteria
4. This review-fix loop can repeat up to **2 times** (3 total review passes max)
5. If still not approved after 3 review passes, commit what you have with a note in the commit message about unresolved review feedback

### Phase 7: Commit

Stage and commit the changes:

1. Run `git status` and `git diff` to review what changed
2. Stage the changed files by name (not `git add .`)
3. Create a commit with conventional format: `refactor:`, `fix:`, `docs:`, `test:`, `perf:`, or `chore:`
4. The commit message should clearly describe what was improved and why

Use a HEREDOC for the commit message:
```bash
git commit -m "$(cat <<'EOF'
refactor: description of changes

- Detail 1
- Detail 2

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>
EOF
)"
```

### Phase 8: Expand Scope (if code changes were minimal)

If the cycle produced fewer than 3 code changes, do NOT stop. Instead, expand your scope:

1. **GitHub issues**: Run `gh issue list` to review open issues. Close stale ones. Create issues for problems you found but couldn't fix.
2. **Dependency audit**: Check for outdated packages with `pnpm outdated`. Flag security issues.
3. **TODO/FIXME scan**: Grep for existing TODOs. Can any be resolved now?
4. **Accessibility review**: Use browserbase to check the UI for accessibility issues.
5. **Pipeline config review**: Read CI/CD config files and check for improvements.
6. **Test coverage gaps**: Look for source files with no corresponding test file.

Create GitHub issues or TODO comments for anything found. Always produce some output — even if it's just a commit with planning comments.

## Project Context

Derive project context from CLAUDE.md (already loaded), package.json, and the repository structure. Specifically:

- **Test command**: Look for `test` script in root package.json (e.g. `pnpm test`, `npm test`, `yarn test`)
- **Lint command**: Look for `lint` script in root package.json (e.g. `pnpm lint`, `tsc --noEmit`)
- **Stack and patterns**: Read CLAUDE.md for conventions, key patterns, and architectural decisions
- **Package structure**: Detect monorepo (workspaces) vs single-package from package.json

If CLAUDE.md is not available or incomplete, run a quick exploration: read package.json, check for tsconfig/eslint configs, and scan a few source files to understand the stack before starting reviews.
