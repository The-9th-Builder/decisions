# ADR-008: Freezed for immutable domain models

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Domain models (data classes representing app state, API responses, parsed data) need value equality, immutability, and a `copyWith` method. Writing these by hand is tedious, error-prone, and produces noisy diffs.

## Decision

Use **Freezed** (`@freezed` annotation + `freezed_annotation` + `freezed` codegen) for all domain and app-level models.

Conventions:
- Raw BSData models live in `lib/models/bsdata/`.
- App-level models live in `lib/models/app/`.
- Riverpod state classes (e.g. `ListFormState`) also use `@freezed`.
- Generated `.freezed.dart` files are committed.

## Alternatives considered

| Option | Why rejected |
|---|---|
| Hand-written `==` / `hashCode` / `copyWith` | Boilerplate-heavy; easy to forget a field; noisy diffs |
| `equatable` | Provides `==` only; still need to write `copyWith` manually |
| `built_value` | More verbose setup; slower codegen; less idiomatic in Flutter ecosystem |
| Dart records (Dart 3+) | No `copyWith`; no union/sealed types; not a full replacement |

## Consequences

- Models are immutable by construction — no accidental mutation of shared state.
- `copyWith` is generated for all fields, including nullable ones.
- Union types (sealed classes with pattern matching) are available via `@freezed` for state modelling.
- Code generation must be re-run after any model change.
- Generated `.freezed.dart` files can be large — normal and expected.
