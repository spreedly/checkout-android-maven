# Spreedly Checkout Android SDK — Distribution Repository

[![GitHub Packages](https://img.shields.io/badge/GitHub%20Packages-published-blue)](https://github.com/spreedly/checkout-android-maven/packages)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

This repository hosts the compiled Android Archive (AAR) packages for the
Spreedly Checkout Android SDK
via [GitHub Packages](https://github.com/features/packages).

Source code and issue tracking live in an internal repository. Public documentation
is available at [docs.spreedly.com](https://docs.spreedly.com).

## Installation

### 1. Configure GitHub Packages authentication

> **Note:** GitHub Packages authentication is required while this repository is
> internal. Once the repository is made public, the credentials block below can
> be removed and packages will resolve without a PAT.

Generate a Personal Access Token (PAT) with `read:packages` scope at
[github.com/settings/tokens](https://github.com/settings/tokens).

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
    implementation(platform("com.spreedly:checkout-bom:1.0.1"))

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
    implementation("com.spreedly:checkout-paymentsheet:1.0.1")
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
- **GPG-signed manifest** (`release-manifest.json.asc`) for manifest integrity verification

To verify an artifact signature:

```bash
# Import Spreedly's public signing key (published on the source repo)
gpg --import spreedly-signing-key.pub

# Confirm the imported key fingerprint matches what Support communicated
# when they shared the key. Compare both values byte-for-byte before trusting it.
gpg --fingerprint

# Verify an AAR signature
gpg --verify checkout-paymentsheet-1.0.1.aar.asc checkout-paymentsheet-1.0.1.aar
```

Contact [mobile-team@spreedly.com](mailto:mobile-team@spreedly.com) for the public signing key.

### Additional verification steps

Once the signing key is imported and its fingerprint confirmed, every release also exposes:

**Signed release tag** -- each tag is signed by the same key:

```bash
git clone https://github.com/spreedly/checkout-android-maven.git
cd checkout-android-maven
git tag -v v1.0.1    # expect "Good signature from ..." matching the fingerprint Support shared
```

**Signed checksum manifest** -- the manifest lists every published artifact (AAR + POM + `.aar.asc`) with its SHA-256 and is itself GPG-signed:

```bash
TAG=v1.0.1
BASE="https://github.com/spreedly/checkout-android-maven/releases/download/${TAG}"

curl -L -o release-manifest.json     "${BASE}/release-manifest.json"
curl -L -o release-manifest.json.asc "${BASE}/release-manifest.json.asc"

gpg --verify release-manifest.json.asc release-manifest.json
```

**SHA-256 round-trip against the manifest** -- once the manifest signature checks out, validate any AAR you've downloaded from GitHub Packages against the trusted hashes:

```bash
# Verify a single artifact
jq -r '.artifacts[] | select(.file == "checkout-paymentsheet-1.0.1.aar") | .sha256' \
  release-manifest.json
sha256sum checkout-paymentsheet-1.0.1.aar

# Or verify everything in one shot
jq -r '.artifacts[] | "\(.sha256)  \(.file)"' release-manifest.json | sha256sum -c
```

## Distribution Strategy

**Current channel: GitHub Packages (Maven)**

- Requires a GitHub Personal Access Token with `read:packages` scope.
- All artifacts are published under the `com.spreedly` group.
- Both release candidates (`-rc.N`) and stable versions are available.
- Dev builds (`-dev.*`) are published for internal testing.

**GitHub Packages visibility:** This repository will be made public, removing
the PAT requirement for consumers.

**Maven Central:** Planned for a future release to provide unauthenticated
public access via standard Maven repositories. Until then, GitHub Packages
is the primary distribution channel.

## Version History

This repository serves as the GitHub Packages namespace for Maven artifacts.
Versions v0.0.1 through v0.0.3 have corresponding GitHub Releases on this
repository. Subsequent versions (0.7.0 through 0.13.0) were published to
GitHub Packages under the same Maven coordinates but GitHub Releases were
created on the source repository
instead. Starting with the next stable release, GitHub Releases with signed
tags, checksum manifests, and SBOMs will be created on both repositories.

See [CHANGELOG.md](CHANGELOG.md) for the full release history.

## Documentation

- [Changelog](CHANGELOG.md)
- [Example App](https://github.com/spreedly/checkout-android-example)
- [Spreedly Docs](https://docs.spreedly.com)

## License

Copyright 2025 Spreedly, Inc.

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.


## Legal

- [Terms of Service](https://legal.spreedly.com/#terms)
- [Privacy Policy](https://legal.spreedly.com/#privacy-policy)
- [License](LICENSE) (Apache 2.0)

