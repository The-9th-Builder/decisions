# ADR-005: Sentry for crash reporting

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Production apps need visibility into crashes and unhandled errors. Without crash reporting, bugs discovered by users are impossible to reproduce or prioritize.

## Decision

Use **Sentry** (`sentry_flutter`) for crash reporting and error monitoring in all apps.

Conventions:
- The DSN is supplied at build time via `--dart-define=SENTRY_DSN=…` (never hardcoded).
- Locally, the DSN is in `.dart_defines.json` (gitignored).
- In CI, the DSN comes from repository secrets.
- When the DSN is absent, Sentry initializes in no-op mode — safe for local dev.
- Release and environment are set at initialization so errors are grouped by version.

## Alternatives considered

| Option | Why rejected |
|---|---|
| Firebase Crashlytics | Ties the project to the Firebase ecosystem; Google account dependency for every app |
| Datadog | Expensive at our scale; overkill for independent apps |
| Custom logging to a backend | Maintenance overhead; no aggregation, symbolication, or alerting built in |
| No crash reporting | Bugs discovered by users go unreported; impossible to improve quality |

## Consequences

- Crashes are captured automatically (Flutter errors, Dart isolate errors, native crashes).
- The DSN secret must be configured in GitHub Actions secrets for each repo.
- Sentry's free tier (5 000 errors/month) is sufficient for current app volumes.
- If a future app does not need crash reporting, it may omit Sentry — but a new ADR should document that choice.
