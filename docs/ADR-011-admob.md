# ADR-011: Google Mobile Ads (AdMob) for monetization

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** my-seeds, skirmish-builder, war-scribe, tank_pilot, sleeves-collection

## Context

Apps with a free tier need a revenue stream for users who do not subscribe. Display advertising is the standard approach for consumer mobile apps.

## Decision

Use **Google Mobile Ads** (`google_mobile_ads`) for banner and interstitial ads.

Conventions:
- Ads are wrapped in `lib/core/ads/` (banner and interstitial wrappers).
- The `adsEnabled` feature flag in `lib/core/config/feature_flags.dart` defaults to `false` — ads are never shown in development or debug builds.
- Ads are only shown to users who have not activated the Pro entitlement (see ADR-010).
- Ad unit IDs are passed via `--dart-define` at build time, never hardcoded.

## Alternatives considered

| Option | Why rejected |
|---|---|
| Unity Ads | Primarily gaming-focused; lower eCPM for utility apps |
| Meta Audience Network | Requires Facebook SDK; privacy implications; more complex setup |
| ironSource / AppLovin | Heavier mediation stacks; overkill for independent apps |
| No ads | Leaves the free tier with no revenue model |

## Consequences

- AdMob covers both Android and iOS from one SDK.
- The `adsEnabled` flag allows shipping builds with ads disabled during review/testing.
- Google AdMob requires an `AdMob App ID` declared in `AndroidManifest.xml` and `Info.plist`.
- Ad performance depends on user geography and category — results vary by app.
