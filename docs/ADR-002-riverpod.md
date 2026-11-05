# ADR-002: Riverpod for state management

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Flutter apps need a way to share and react to state across the widget tree. The built-in `setState` / `InheritedWidget` approach does not scale past a single screen. A solution was needed that is testable, type-safe, and handles async data cleanly.

## Decision

Use **Riverpod** with code generation (`@riverpod` / `@Riverpod` annotations via `riverpod_annotation` + `riverpod_generator`). Providers are never hand-written.

Key conventions:
- `keepAlive: true` for infrastructure providers (database, DAOs, sync services).
- Auto-dispose (default) for feature and screen providers.
- UI always consumes `AsyncValue` via `.when(data:, loading:, error:)`.

## Alternatives considered

| Option | Why rejected |
|---|---|
| Provider (original) | Superseded by Riverpod; weaker compile-time safety |
| BLoC / flutter_bloc | More boilerplate; event/state separation is overhead for these app sizes |
| MobX | Requires annotations + codegen; less idiomatic in Flutter ecosystem |
| GetX | Mixes routing, state, DI in one package; harder to test; opinionated magic |
| setState only | Does not scale across screens; no dependency injection |

## Consequences

- State is globally accessible and type-safe with no `BuildContext` required at the provider level.
- Auto-dispose frees memory automatically when a screen is left.
- `ref.invalidate()` is the standard way to force a provider to re-fetch.
- Code generation must be re-run (`dart run build_runner build`) after any provider change.
- Generated `.g.dart` files are committed to the repository.
