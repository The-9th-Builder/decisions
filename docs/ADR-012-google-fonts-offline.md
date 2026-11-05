# ADR-012: Google Fonts bundled offline

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Apps use custom typography (e.g. Cinzel, Inter, DM Mono in WarScribe) for brand identity. The `google_fonts` package can download fonts at runtime, but that adds latency, fails without connectivity, and is inconsistent with an offline-first approach.

## Decision

Use the **`google_fonts`** package with **`GoogleFonts.config.allowRuntimeFetching = false`**.

Font files (`.ttf`) are bundled in `assets/fonts/` and registered automatically by `google_fonts` — no `fonts:` section in `pubspec.yaml`.

To add a new font: download the TTF files to `assets/fonts/` and reference the font via `GoogleFonts.<name>()`. No `pubspec.yaml` change is needed.

## Alternatives considered

| Option | Why rejected |
|---|---|
| `google_fonts` with runtime fetching enabled | Requires network on first use; inconsistent across offline scenarios; font flash on cold start |
| Manual `fonts:` in `pubspec.yaml` | Works, but requires more boilerplate and loses the `google_fonts` API convenience |
| System fonts only | No custom typography; brand identity lost |
| Self-hosting via a CDN | Unnecessary for a mobile app; adds infrastructure |

## Consequences

- Typography is consistent on first launch, offline, and in screenshot tests.
- Font files increase the app bundle size (~50–200 KB per font family).
- `allowRuntimeFetching = false` will throw if a font is referenced but not bundled — a fail-fast behaviour that surfaces missing assets during development.
- Never add a `fonts:` section in `pubspec.yaml` for fonts managed by `google_fonts`.
