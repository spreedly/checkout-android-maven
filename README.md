# Spreedly Checkout Android SDK — Distribution Repository

[![GitHub Packages](https://img.shields.io/badge/GitHub%20Packages-published-blue)](https://github.com/spreedly/checkout-android-maven/packages)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

This repository hosts the compiled Android Archive (AAR) packages for the
[Spreedly Checkout Android SDK](https://github.com/spreedly/checkout-android-sdk)
via [GitHub Packages](https://github.com/features/packages).

Source code, issue tracking, and documentation live in the
[source repository](https://github.com/spreedly/checkout-android-sdk).

## Installation

### 1. Configure GitHub Packages authentication

GitHub Packages requires a Personal Access Token (PAT) with `read:packages` scope.
Generate one at [github.com/settings/tokens](https://github.com/settings/tokens).

Add your credentials to `~/.gradle/gradle.properties` (user-level, not committed):

```properties
gpr.usr=YOUR_GITHUB_USERNAME
gpr.key=ghp_YOUR_PERSONAL_ACCESS_TOKEN
```

### 2. Add the Maven repository

In your project's `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://maven.pkg.github.com/spreedly/checkout-android-maven")
            credentials {
                username = providers.gradleProperty("gpr.usr").orNull
                    ?: System.getenv("GITHUB_USERNAME")
                password = providers.gradleProperty("gpr.key").orNull
                    ?: System.getenv("GITHUB_TOKEN")
            }
        }
    }
}
```

### 3. Add dependencies

**Option A — Bill of Materials (recommended):**

```kotlin
dependencies {
    implementation(platform("com.spreedly:checkout-bom:0.13.0"))

    // Pick the modules you need (versions managed by the BOM):
    implementation("com.spreedly:checkout-paymentsheet")
    implementation("com.spreedly:checkout-threeds")           // 3D Secure
    implementation("com.spreedly:checkout-braintree-apm")     // PayPal, Venmo
    implementation("com.spreedly:checkout-stripe-apm")        // iDEAL, Bancontact, EPS, etc.
}
```

**Option B — Explicit versions:**

```kotlin
dependencies {
    implementation("com.spreedly:checkout-paymentsheet:0.13.0")
}
```

## Modules

| Artifact | Description |
|----------|-------------|
| `checkout-payments-core` | Core payment processing, tokenization, and validation |
| `checkout-hostedfields` | Secure individual payment field components |
| `checkout-paymentsheet` | Pre-built payment sheet UI (depends on core + hosted fields) |
| `checkout-threeds` | 3D Secure authentication (Global 3DS and Gateway-Specific) |
| `checkout-braintree-apm` | Braintree PayPal and Venmo |
| `checkout-stripe-apm` | Stripe Alternative Payment Methods (iDEAL, Bancontact, EPS, SEPA, P24) |
| `checkout-bom` | Bill of Materials — aligns all module versions |

## Package Security

All published AAR artifacts are **GPG-signed** (`.aar.asc` signature files accompany each
artifact). Stable releases include:

- **SHA-256 checksum manifest** (`release-manifest.json`) attached to GitHub Releases
- **SBOM** (CycloneDX format, `sbom.json` / `sbom.xml`) attached to GitHub Releases
- **GPG-signed manifest** (`release-manifest.json.sig`) for manifest integrity verification

To verify an artifact signature:

```bash
# Import Spreedly's public signing key (published on the source repo)
gpg --import spreedly-signing-key.pub

# Verify an AAR signature
gpg --verify checkout-paymentsheet-0.13.0.aar.asc checkout-paymentsheet-0.13.0.aar
```

See [SIGNATURE_VERIFICATION.md](https://github.com/spreedly/checkout-android-sdk/blob/main/docs/development/SIGNATURE_VERIFICATION.md)
for full verification instructions.

## Distribution Strategy

**Current channel: GitHub Packages (Maven)**

- Requires a GitHub Personal Access Token with `read:packages` scope.
- All artifacts are published under the `com.spreedly` group.
- Both release candidates (`-rc.N`) and stable versions are available.
- Dev builds (`-dev.*`) are published for internal testing.

**Maven Central: Not currently planned.**
GitHub Packages is the sole distribution channel. This avoids unauthenticated
access to pre-GA artifacts and simplifies key management. The decision will be
revisited when the SDK reaches general availability.

## Version History

This repository serves as the GitHub Packages namespace for Maven artifacts.
Versions v0.0.1 through v0.0.3 have corresponding GitHub Releases on this
repository. Subsequent versions (0.7.0 through 0.13.0) were published to
GitHub Packages under the same Maven coordinates but GitHub Releases were
created on the [source repository](https://github.com/spreedly/checkout-android-sdk/releases)
instead. Starting with the next stable release, GitHub Releases with signed
tags, checksum manifests, and SBOMs will be created on both repositories.

See [CHANGELOG.md](CHANGELOG.md) for the full release history.

## Documentation

- [Integration Guide](https://github.com/spreedly/checkout-android-sdk#installation)
- [API Documentation](https://github.com/spreedly/checkout-android-sdk/tree/main/docs)
- [Example App](https://github.com/spreedly/checkout-android-example)
- [Signature Verification](https://github.com/spreedly/checkout-android-sdk/blob/main/docs/development/SIGNATURE_VERIFICATION.md)

## License

Copyright 2025 Spreedly, Inc.

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.

