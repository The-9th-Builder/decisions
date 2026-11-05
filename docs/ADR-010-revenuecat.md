# ADR-010: RevenueCat for in-app subscriptions

**Status:** Accepted  
**Date:** 2024-01-01  
**Applies to:** my-seeds, skirmish-builder, war-scribe, tank_pilot, sleeves-collection

## Context

Several apps offer a "Pro" tier that removes ads or unlocks features. Implementing and validating in-app purchases directly via StoreKit (iOS) and Play Billing (Android) requires platform-specific code, server-side receipt validation, and significant maintenance effort.

## Decision

Use **RevenueCat** (`purchases_flutter`) as the single cross-platform subscription and purchase management layer.

Conventions:
- RevenueCat is initialized at app startup with the public SDK key.
- Entitlement checks are performed via `Purchases.getCustomerInfo()`.
- The Pro entitlement identifier is `pro` by convention across all apps.
- Feature flags (`adsEnabled`, `subscriptionsEnabled` in `lib/core/config/feature_flags.dart`) gate the entire monetization stack — both default to `false` in development.

## Alternatives considered

| Option | Why rejected |
|---|---|
| StoreKit + Play Billing directly | Platform-specific code (2× implementation); no cross-platform subscriber view; server-side validation required |
| Adapty | Similar offering; RevenueCat has larger community and better Flutter SDK |
| Qonversion | Less mature Flutter SDK at the time of adoption |
| One-time purchase only | Subscriptions provide more predictable revenue and easier trial flows |

## Consequences

- A single SDK covers iOS, Android, and future platforms.
- Receipt validation is handled server-side by RevenueCat — no backend required.
- RevenueCat's free tier covers up to $2 500 MRR per app.
- Both `adsEnabled` and `subscriptionsEnabled` must be set to `true` in a release build to activate monetization.
- The RevenueCat API key is passed at runtime (not hardcoded) via `--dart-define`.
