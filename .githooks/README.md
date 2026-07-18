# Git hooks — pre-commit secret scan

The `pre-commit` hook blocks a commit that introduces a secret into staged changes.
This repository hosts marketplace solution content, so nothing credential-shaped may
ever land in it.

## One-time setup (per clone)

Hooks live here, not in `.git/hooks`, so they are version-controlled. Point git at them:

```sh
git config core.hooksPath .githooks
```

(Run from the repo root. This is a local config — every clone must set it once. CI can
enforce it, or add it to your onboarding script.)

## How it works

The hook has two modes, picked automatically:

1. **gitleaks present** — runs `gitleaks protect --staged --redact` over the staged
   changes using gitleaks' full, maintained ruleset. **This is the recommended setup.**
   Install it:
   - Windows: `winget install gitleaks` (or `scoop install gitleaks`)
   - macOS: `brew install gitleaks`
   - Go: `go install github.com/gitleaks/gitleaks/v8@latest`

2. **gitleaks absent** — falls back to a built-in scan of *staged additions* for a small
   set of high-confidence patterns (GitHub tokens incl. `github_pat_`, AWS access key IDs,
   PEM private-key headers, Slack tokens, Google API keys). This guarantees baseline
   protection even before anyone installs gitleaks, but it is **not** a substitute for the
   full ruleset — install gitleaks.

Either way the hook only inspects what you're about to commit, so it won't block normal
work over pre-existing content.

## Handling a finding

- **Real secret:** remove it. Put the value in an environment variable / secret store and
  reference it from config (e.g. `github.token=${GITHUB_TOKEN:}`), then re-stage.
- **Verified false positive:** append the comment `gitleaks:allow` to the offending line,
  or, as a last resort, bypass the hook for that commit with `git commit --no-verify`.
- **Recurring false positives (gitleaks mode):** add a `.gitleaks.toml` at the repo root
  that `extends` the default config and allowlists the specific path/regex. See the
  gitleaks docs for the allowlist format.

## Scope

Every clone of this repository should set `core.hooksPath` as above before
committing solution content.
