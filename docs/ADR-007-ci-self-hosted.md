# ADR-007: CI on self-hosted GitHub Actions runners

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Flutter CI pipelines (build, test, sign, deploy) are expensive on GitHub-hosted runners: macOS runners cost 10× the Linux rate, and minute quotas run out quickly. Build times are also slower because Flutter must be downloaded and cached on each run.

## Decision

Run all CI on **self-hosted GitHub Actions runners** labeled `flutter`.

The runner pool includes:
- One macOS machine (for iOS and macOS builds, code signing).
- One ARM64 Linux machine (for Android builds and fast analysis/test jobs).

All workflow files reference runners with:
```yaml
runs-on: [self-hosted, flutter]
```

## Alternatives considered

| Option | Why rejected |
|---|---|
| GitHub-hosted macOS runners | 10× cost; slower (Flutter re-downloaded each run); minute limits |
| Codemagic | Paid per build minute; less control over environment; another vendor dependency |
| Bitrise | Same cost concerns as Codemagic |
| GitHub-hosted Linux only | Cannot build or sign iOS/macOS apps |

## Consequences

- CI builds are fast (Flutter pre-installed, no cold cache).
- No per-minute cost from GitHub — only the infrastructure running the runners.
- Runner maintenance (OS updates, Flutter upgrades, Xcode updates) is manual.
- If a runner goes offline, CI is blocked until it is restored.
- Secrets (keystore, certificates, DSNs) live on the runner machines and in GitHub repository secrets.
