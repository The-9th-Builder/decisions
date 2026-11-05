# Architecture Decision Records

This repository documents the architectural decisions shared across all The 9th Builder apps.

## What is an ADR?

An Architecture Decision Record captures a significant choice made during development — the context, the decision taken, the alternatives considered, and the consequences. It is written once and kept permanently, even if the decision is later superseded (the status is updated, not the document deleted).

## Apps covered

| App | Repository |
|---|---|
| WarScribe | war-scribe |
| My Seeds | my-seeds |
| Skirmish Builder | skirmish-builder |
| Speed Calculator | speed-calculator |
| SleevIt | sleeves-collection |
| TankPilot | tank_pilot |

## Index

| # | Title | Status |
|---|---|---|
| [ADR-001](docs/ADR-001-flutter.md) | Flutter as cross-platform framework | Accepted |
| [ADR-002](docs/ADR-002-riverpod.md) | Riverpod for state management | Accepted |
| [ADR-003](docs/ADR-003-gorouter.md) | GoRouter for navigation | Accepted |
| [ADR-004](docs/ADR-004-drift.md) | Drift for local SQLite persistence | Accepted |
| [ADR-005](docs/ADR-005-sentry.md) | Sentry for crash reporting | Accepted |
| [ADR-006](docs/ADR-006-store-metadata.md) | Store metadata structure | Accepted |
| [ADR-007](docs/ADR-007-ci-self-hosted.md) | CI on self-hosted GitHub Actions runners | Accepted |
| [ADR-008](docs/ADR-008-freezed.md) | Freezed for immutable domain models | Accepted |
| [ADR-009](docs/ADR-009-localization.md) | ARB + flutter gen-l10n for localization | Accepted |
| [ADR-010](docs/ADR-010-revenuecat.md) | RevenueCat for in-app subscriptions | Accepted |
| [ADR-011](docs/ADR-011-admob.md) | Google Mobile Ads (AdMob) for monetization | Accepted |
| [ADR-012](docs/ADR-012-google-fonts-offline.md) | Google Fonts bundled offline | Accepted |
| [ADR-013](docs/ADR-013-play-deploy.md) | play_deploy.py for Play Store deployment | Accepted |

## Template

```markdown
# ADR-NNN: Title

**Status:** Proposed | Accepted | Superseded by ADR-NNN | Deprecated  
**Date:** YYYY-MM-DD  
**Applies to:** All apps | [app list]

## Context

Why does this decision need to be made?

## Decision

What was decided?

## Alternatives considered

| Option | Why rejected |
|---|---|
| ... | ... |

## Consequences

What becomes easier, harder, or different as a result?
```
