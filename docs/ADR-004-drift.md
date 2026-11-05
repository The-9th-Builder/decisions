# ADR-004: Drift for local SQLite persistence

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

All apps need structured local data storage that survives app restarts, supports relational data, and can be queried efficiently. A key-value store is not sufficient.

## Decision

Use **Drift** (formerly Moor) on top of SQLite for all structured local persistence.

Conventions:
- Tables and DAOs are defined in `lib/core/database/`.
- The `AppDatabase` class is the single Drift database instance, provided via Riverpod (`appDatabaseProvider`, `keepAlive: true`).
- DAOs are exposed as separate providers (`keepAlive: true`).
- Migrations are written manually in `AppDatabase.migration`.
- Generated `.g.dart` files are committed.

## Alternatives considered

| Option | Why rejected |
|---|---|
| sqflite (raw) | No type safety, manual column mapping, no reactive streams |
| Hive | Key-value only; poor fit for relational data (entries, lists, quantities…) |
| Isar | Promising but less mature; no SQL fallback; migration story weaker |
| ObjectBox | Commercial license for some features; heavier binary size |
| SharedPreferences | Key-value only; not suitable for collections |
| Firebase / Firestore | Cloud-first; offline-first use case requires SQLite locally |

## Consequences

- Queries are type-safe and checked at compile time.
- Reactive streams (`watchAll()`, `watchById()`) integrate naturally with Riverpod.
- Schema changes require a migration step — forgetting this causes a runtime crash.
- Code generation must be re-run after any table or DAO change.
