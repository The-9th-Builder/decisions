# ADR-009: ARB + flutter gen-l10n for localization

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Apps target users in 15 locales (see ADR-006). UI strings must be translated and accessed in a type-safe way. Hardcoded strings make translation impossible and clutter the diff history.

## Decision

Use the official Flutter localization toolchain:

- **ARB files** (`lib/core/l10n/app_en.arb`, `app_fr.arb`, …) as the source of truth.
- `app_en.arb` is the reference file; all other locales are translations of it.
- **`flutter gen-l10n`** generates the `AppLocalizations` class from the ARB files.
- Strings are accessed via `AppLocalizations.of(context)` (or `context.l10n` via extension).
- Run `flutter gen-l10n` after any change to an ARB file.

Typographic curly quotes (`"`) must not appear inside ARB string values — they break JSON parsing.

## Alternatives considered

| Option | Why rejected |
|---|---|
| `easy_localization` | Third-party; loads strings at runtime from JSON/YAML; less type-safe |
| `intl_utils` / `flutter_intl` | IDE plugin dependency; generates similar output but with more config overhead |
| GetX i18n | Tied to the GetX ecosystem (see ADR-002); no compile-time key checking |
| Hardcoded strings | Untranslatable; rejected from the start |

## Consequences

- All user-visible strings are type-safe — a missing translation key is a compile error.
- Adding a new language requires a new `app_<locale>.arb` file and a `flutter gen-l10n` run.
- Pluralization and argument interpolation are handled natively by the ICU message format.
- The generated `l10n/` output is not committed (it is regenerated at build time).
