# ADR-014 — Android Build Standards

**Status:** Accepted  
**Date:** 2026-11-10

## Context

All Flutter apps target Android via a shared `build.gradle.kts` / `settings.gradle.kts` structure. The Google Play Store surfaces recommendations when build tooling or configuration is suboptimal. Three recurring gaps were identified across the portfolio:

1. **Android Gradle Plugin (AGP) version** — apps were on AGP 8.x; Play Store recommends AGP 9.0+.
2. **Resource shrinking** — `isShrinkResources` was not enabled, leaving unused resources in release APKs.
3. **Edge-to-edge APIs** — deprecated `setStatusBarColor` / `setNavigationBarColor` / `LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES` flagged for Android 15 (SDK 35+). These originate in third-party libraries (Flutter engine, GMS consent SDK), not in app code.

## Decision

### AGP version

All apps target **AGP 9.1.0** (aligned with `tank_pilot`, which was the first to migrate). The Gradle wrapper stays at 8.14 (minimum required for AGP 9.x is 8.10).

```kotlin
// android/settings.gradle.kts
plugins {
    id("com.android.application") version "9.1.0" apply false
    ...
}
```

### R8 resource shrinking

All apps enable both `isMinifyEnabled` and `isShrinkResources` in the release build type:

```kotlin
buildTypes {
    release {
        isMinifyEnabled = true
        isShrinkResources = true
        proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
    }
}
```

`isShrinkResources` requires `isMinifyEnabled = true` to be effective.

### Edge-to-edge

Flutter apps using `SafeArea` and `MediaQuery.viewPadding` are already compliant with Android 15 edge-to-edge requirements. The deprecated window APIs flagged by Play Store come from third-party dependencies (Flutter engine, Google consent SDK) and cannot be removed at the app level — they will disappear as upstream libraries are updated.

No app-level code change is required for edge-to-edge. Teams should **test on Android 15** when targeting SDK 35+ to confirm insets are handled correctly.

## Apps covered

| App | AGP before | AGP after | shrinkResources |
|---|---|---|---|
| war-scribe | 8.11.1 | 9.1.0 | added |
| my-seeds | 8.11.1 | 9.1.0 | added |
| speed-calculator | 8.11.1 | 9.1.0 | added |
| skirmish-builder | 8.11.1 | 9.1.0 | added (+ minify) |
| tank_pilot | 9.1.0 | — (reference) | pending |

## Consequences

- Release APKs will be smaller (unused resources stripped).
- Build times may increase slightly during the first build after the AGP upgrade (Gradle caches warm up).
- Play Store "R8 optimization" recommendation is resolved.
- AGP upgrades should be tracked in `settings.gradle.kts` across all apps when new major versions are released.
