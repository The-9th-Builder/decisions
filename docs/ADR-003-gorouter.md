# ADR-003: GoRouter for navigation

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Flutter's imperative `Navigator.push` does not support deep links, URL-based routing, or shell routes (persistent bottom navigation bar) cleanly. A declarative router was needed.

## Decision

Use **GoRouter** for all navigation. The router is defined once in `lib/routes/app_router.dart`.

Standard patterns:
- `ShellRoute` for the bottom navigation bar scaffold.
- Full-screen routes (no bottom nav) declared outside the shell.
- `context.push()` to navigate forward, `context.pop()` to go back.
- Never mix `Navigator.push` with GoRouter in the same app.

## Alternatives considered

| Option | Why rejected |
|---|---|
| Navigator 2.0 (manual) | Very verbose; requires implementing `RouterDelegate` and `RouteInformationParser` by hand |
| auto_route | Heavier code generation; GoRouter is now the Flutter team's recommended solution |
| beamer | Less maintained since GoRouter became official |
| GetX routing | Tied to the GetX ecosystem (see ADR-002) |

## Consequences

- Deep links and URL-based navigation work on all platforms including web.
- Shell routes give the persistent bottom nav bar with zero extra state.
- Route definitions are centralized and easy to audit.
- `context.push()` returns a future; `await`-ing it and then calling `context.pop()` is a common mistake that pops the calling screen — avoid it.
