# Validation

The orchestrator runs these command blocks against each arm's worktree at the named phase. Commands must be invoked identically for every arm. Capture stdout, stderr, and exit code verbatim into `artifacts/arm-<name>/validation-<phase>.md`.

All commands run with the arm's worktree as `cwd`.

## scaffold

```
ls -la
wc -l index.html
grep -cE '<(header|nav|main|footer|section|article|aside)\b' index.html
grep -c 'alt=' index.html
grep -cE '<h[1-6]\b' index.html
test -f index.html && echo "index.html: present" || echo "index.html: missing"
tidy -errors -q index.html
```

Notes: `tidy`'s exit code of 1 means warnings, 2 means errors — capture exit code but do not fail the run on non-zero.

## style

```
ls -la
wc -l index.html styles.css
grep -cE '<(header|nav|main|footer|section|article|aside)\b' index.html
grep -c 'alt=' index.html
grep -c 'aria-' index.html
grep -cE '@media' styles.css
grep -cE '^\s*(\.|#|[a-zA-Z][a-zA-Z0-9_-]*)\s*[{,]' styles.css
test -f styles.css && echo "styles.css: present" || echo "styles.css: missing"
grep -q 'styles.css' index.html && echo "link: ok" || echo "link: missing"
tidy -errors -q index.html
```

## polish

```
ls -la
wc -l index.html styles.css
grep -cE '<(header|nav|main|footer|section|article|aside)\b' index.html
grep -c 'alt=' index.html
grep -c 'aria-' index.html
grep -cE ':focus|:focus-visible' styles.css
grep -cE '@media' styles.css
grep -cE '^\s*(\.|#|[a-zA-Z][a-zA-Z0-9_-]*)\s*[{,]' styles.css
tidy -errors -q index.html
```

## Notes on parity

The orchestrator must run **every command above** for **every arm**, in the listed order, and capture output verbatim. Do not skip commands for any arm even if prior commands have non-zero exit. If a binary like `tidy` is not installed, the run should be aborted (not silently skipped), because skipping only some runs breaks observer parity across a whole experiment series.
