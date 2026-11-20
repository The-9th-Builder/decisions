# ADR-009: ARB + flutter gen-l10n for localization

**Status:** Accepted (amended 2026-11-20: the app ships the 15 store languages)  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Apps target users in 15 locales (see ADR-006). UI strings must be translated and accessed in a type-safe way. Hardcoded strings make translation impossible and clutter the diff history.

Until November 2026 the apps were only translated into 10 of these languages: users reading the Japanese, Dutch, Brazilian Portuguese, Turkish or Arabic store listing got an English app. The app and its listing now cover the same 15 languages.

## Decision

Use the official Flutter localization toolchain:

- **ARB files** (`lib/core/l10n/app_en.arb`, `app_fr.arb`, …) as the source of truth.
- `app_en.arb` is the reference file; all other locales are translations of it.
- **`flutter gen-l10n`** generates the `AppLocalizations` class from the ARB files.
- Strings are accessed via `AppLocalizations.of(context)` (or `context.l10n` via extension).
- Run `flutter gen-l10n` after any change to an ARB file.

Typographic curly quotes (`"`) must not appear inside ARB string values — they break JSON parsing.

### App languages

Every app ships these 15 ARB files, one per store locale of ADR-006:

| ARB file | Store locale | Language |
|---|---|---|
| `app_en.arb` | `en-US` | English (reference) |
| `app_fr.arb` | `fr-FR` | French |
| `app_de.arb` | `de-DE` | German |
| `app_es.arb` | `es-ES` | Spanish |
| `app_it.arb` | `it-IT` | Italian |
| `app_pl.arb` | `pl-PL` | Polish |
| `app_pt.arb` | `pt-PT` | Portuguese (Portugal) |
| `app_pt_BR.arb` | `pt-BR` | Portuguese (Brazil) |
| `app_ru.arb` | `ru-RU` | Russian |
| `app_ko.arb` | `ko-KR` | Korean |
| `app_zh.arb` | `zh-CN` | Chinese (Simplified) |
| `app_ja.arb` | `ja-JP` | Japanese |
| `app_nl.arb` | `nl-NL` | Dutch |
| `app_tr.arb` | `tr-TR` | Turkish |
| `app_ar.arb` | `ar` | Arabic (right-to-left) |

- `app_pt_BR.arb` is a full translation in Brazilian Portuguese (not only the keys that differ): gen-l10n falls back to `app_pt.arb` for a missing key, which would mix the two variants.
- The in-app language picker lists the 15 languages, each in its own name (`Português (Brasil)`, `العربية`, …).
- A unit test checks that every ARB file has exactly the keys of `app_en.arb` and the same placeholders.

### Right-to-left (Arabic)

`MaterialApp` mirrors the layout for Arabic on its own; the code must not fight it:

- Use `EdgeInsetsDirectional`, `AlignmentDirectional`, `PositionedDirectional` and `start`/`end` instead of `left`/`right` whenever the side matters.
- Icons that point somewhere (back, forward, chevrons) use the `Icons.arrow_back` family, which Flutter mirrors.
- Numbers, dates and prices go through `intl` with the current locale, never through string concatenation.

### Store screenshots

The screenshot tool of each app (integration test on demo data) loops over the 15 languages and writes `store/screenshots/android/phone/<store locale>/`, so every listing shows the app in its own language. The feature graphic is generated per locale as well. Fonts that lack a script (Arabic, Japanese, Korean, Chinese) use a system font that covers it, and Arabic text is shaped right-to-left (Pillow with libraqm).

## Alternatives considered

| Option | Why rejected |
|---|---|
| `easy_localization` | Third-party; loads strings at runtime from JSON/YAML; less type-safe |
| `intl_utils` / `flutter_intl` | IDE plugin dependency; generates similar output but with more config overhead |
| GetX i18n | Tied to the GetX ecosystem (see ADR-002); no compile-time key checking |
| Hardcoded strings | Untranslatable; rejected from the start |
| Translate only the store listing for the 5 extra locales | The listing promised an app the user could not read |

## Consequences

- All user-visible strings are type-safe — a missing translation key is a compile error.
- Adding a new language requires a new `app_<locale>.arb` file and a `flutter gen-l10n` run, plus its store locale (ADR-006), its entry in the language picker and in the screenshot tool.
- Every new string is written in the 15 languages in the same change.
- Pluralization and argument interpolation are handled natively by the ICU message format.
- The generated `l10n/` output is not committed (it is regenerated at build time).
