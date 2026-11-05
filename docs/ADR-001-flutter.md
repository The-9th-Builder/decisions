# ADR-001: Flutter as cross-platform framework

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Each app targets Android and iOS, with some also targeting macOS. Writing and maintaining separate native codebases (Swift/Kotlin) for each app and each platform would multiply the development effort. A cross-platform solution was needed.

## Decision

Use **Flutter** (Dart) as the single framework for all apps, targeting Android, iOS, and macOS from one codebase.

## Alternatives considered

| Option | Why rejected |
|---|---|
| React Native | Less performant rendering, JavaScript ecosystem complexity, weaker desktop support |
| Kotlin Multiplatform | Shares logic but not UI — still requires platform-specific UI code |
| Native per platform | 2–3× the development effort for the same feature set |
| Xamarin / MAUI | Smaller community, weaker tooling, less Flutter-class UI fidelity |

## Consequences

- Single codebase covers Android, iOS, and macOS.
- Dart is the only language required.
- UI is rendered by Flutter's own engine — consistent look across platforms.
- Access to platform-specific APIs requires a plugin or a method channel.
- All future apps in this org are Flutter apps by default; deviating requires a new ADR.
