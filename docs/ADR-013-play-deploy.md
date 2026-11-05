# ADR-013: play_deploy.py for Play Store deployment

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** All apps

## Context

Releasing a new version to the Play Store requires uploading an AAB binary, updating store metadata (title, descriptions, changelogs), and promoting to the right track (internal → production). Doing this manually in the Play Console is slow, error-prone, and not reproducible.

## Decision

Use a custom Python script **`tool/play_deploy.py`** that:

1. Reads listing texts from `store/metadata/<locale>/*.txt` (see ADR-006).
2. Uploads the signed AAB to the Play Store via the Google Play Developer API.
3. Updates all localized metadata on the listing.
4. Promotes the release to the target track (`internal` by default, `production` via flag).

The script is invoked by the CI release workflow and can also be run locally.

Authentication uses a Google service account JSON key (`GOOGLE_PLAY_JSON_KEY` secret).

## Alternatives considered

| Option | Why rejected |
|---|---|
| Manual Play Console | Not reproducible; slow; no audit trail in git |
| Fastlane Supply | Additional Ruby/Fastlane dependency stack; custom script gives full control with fewer dependencies |
| GitHub Actions marketplace Play Store action | Less control; relies on third-party action maintenance |
| Google Play Developer API directly (curl/gcloud) | Same outcome as the script but without structure or reuse |

## Consequences

- Releases are fully automated and reproducible from CI.
- Metadata changes (translations, descriptions) go through a git PR before reaching the store.
- The `GOOGLE_PLAY_JSON_KEY` secret must be configured in GitHub Actions for each repository.
- The script enforces the Play Store character limits (title ≤ 30, short ≤ 80, full ≤ 4 000) and fails fast if they are exceeded.
- App Store (Apple) deployment is not yet automated — still done manually via Xcode / Transporter.
