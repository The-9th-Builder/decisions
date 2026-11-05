# ADR-006: Store metadata structure

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Each app needs a Play Store listing (and App Store listing) in multiple languages. Managing these texts manually in the developer consoles is error-prone, not version-controlled, and hard to review or translate collaboratively.

## Decision

Store all Play Store listing texts in the repository under `store/metadata/<locale>/`, with one file per field:

```
store/
  metadata/
    en-US/
      title.txt              # ≤ 30 characters
      short_description.txt  # ≤ 80 characters
      full_description.txt   # ≤ 4 000 characters
    fr-FR/
      …
```

The standard locale set for all apps is: `en-US`, `fr-FR`, `de-DE`, `es-ES`, `it-IT`, `ko-KR`, `pl-PL`, `pt-PT`, `ru-RU`, `zh-CN`, `ja-JP`, `pt-BR`, `tr-TR`, `nl-NL`, `ar`.

Deployment to the Play Store is automated via `tool/play_deploy.py` (see ADR-013).

## Alternatives considered

| Option | Why rejected |
|---|---|
| Manual Play Console edits | Not version-controlled; no review process; easy to lose history |
| Single JSON file | Harder to diff per locale; character-limit validation is less obvious |
| Fastlane Supply convention | Same structure, but adds the Fastlane dependency; we use a custom script instead |

## Consequences

- Listing texts are version-controlled and reviewable as normal diffs.
- Adding a new language is a PR with new locale directories.
- The 30/80/4 000 character limits are enforced at deploy time by `play_deploy.py`.
- App Store metadata is not yet automated — this ADR covers Play Store only.
