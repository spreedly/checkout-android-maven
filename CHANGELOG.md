# Changelog

All notable changes to the Spreedly Android SDK are documented in this file.

The canonical changelog is maintained in the
[source repository](https://github.com/spreedly/checkout-android-sdk/blob/main/docs/CHANGELOG.md).
This file is synced from there at release time.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.13.0] - 2026-03-31

### Added

- **ACH Bank Account Payments**: Tokenize bank accounts via `SpreedlyBankAccountBottomSheet` (pre-built UI), `BankAccountSheet` (custom layouts), or headless `sdk.createBankAccount()`. Includes routing/account number validators, configurable field visibility via `BankAccountFieldConfig` (Default, Minimal, Full presets), separate or full name modes, and secure auto-clear for account numbers.
- **`BankAccountFieldConfig`**: Data class controlling which optional fields appear in the bank account form (account type, holder type, bank name, name display mode).
- **`FormFieldType.ROUTING_NUMBER` / `ACCOUNT_NUMBER`**: New form field types for bank account secure fields with built-in validation.
- **`PaymentMethodType` enum**: Distinguishes `CREDIT_CARD` and `BANK_ACCOUNT` payment methods.

### Changed

- **Build toolchain**: Kotlin **2.3.10**, KSP **2.3.6** (KSP2 versioning), Android Gradle Plugin **8.13.2**.

## [0.12.1] - 2026-03-17

### Fixed

- **Stripe APM SEPA Direct Debit**: Payments no longer show Failed when transaction state is `"processing"`. The SDK now treats `"processing"` as non-terminal and continues polling.

## [0.12.0] - 2026-03-13

### Added

- **`source` field on payment method creation**: All payment method creation requests now include `source` identifying the checkout SDK and platform.

### Changed

- **`sdkPlatform` is now a `SdkPlatform` enum**: Replaced the `String` parameter on `SpreedlySDKInitOptions` with a type-safe `SdkPlatform` enum (`ANDROID`, `REACT_NATIVE`). **Breaking**: callers passing `sdkPlatform = "react_native"` must change to `sdkPlatform = SdkPlatform.REACT_NATIVE`.

## [0.11.3] - 2026-03-13

### Added

- **`User-Agent` header on API requests**: All outbound HTTP requests to `core.spreedly.com` now include `User-Agent: Spreedly-Android-SDK/{version}`.

## [0.11.2] - 2026-03-12

### Added

- **Auth Tab for 3DS browser flows**: All gateway-specific 3DS flows now use Auth Tab on Chrome 137+ with Chrome Custom Tab fallback.
- Separate `ActivityResultLauncher` instances for fingerprint and challenge flows.

### Changed

- `androidx.browser` dependency bumped from 1.8.0 to 1.9.0 to enable Auth Tab.
- Card number fields now allow paste (copy/cut remain blocked for PCI compliance).

### Fixed

- Auth Tab resolves the known issue where users could navigate from a Chrome Custom Tab to the Chrome browser during 3DS challenges.

## [0.11.1] - 2026-03-11

### Added

- **`sdk_platform` global telemetry attribute**: New `sdkPlatform` field on `SpreedlySDKInitOptions` (default `"android"`).

## [0.11.0] - 2026-03-11

### Added

- **3DS Module** (`checkout-threeds`): Extracted all 3DS functionality into a dedicated module.
- SDK-owned `OffsiteReturnActivity` with scheme `${applicationId}.spreedlyoffsite`.
- **Datadog telemetry**: Structured telemetry events covering SDK initialization, payment method creation, recache, 3DS, APM checkout, API requests, hosted field interactions, offsite payments, and validation failures.
- **Event sanitization**: `LogSanitizer` strips card numbers, CVVs, API keys, and signatures from log messages.

### Changed

- **Breaking: 3DS package rename** -- `com.spreedly.sdk.threeds.*` is now `com.spreedly.threeds.*`.
- **Breaking: Offsite deep link scheme changed** -- The SDK now owns `OffsiteReturnActivity`.
- **Breaking: 3DS dependency moved** -- Merchants using 3DS must add `com.spreedly:checkout-threeds`.
- **Breaking: `SavedCardInfo.cardBrand` renamed to `cardholderName`**.

### Removed

- Fat AAR build task and `scripts/publish-fat-aar.sh`.
- OWASP Dependency Check scan job.

## [0.10.4] - 2026-03-03

> **Note:** Versions 0.10.2 and 0.10.3 were internal builds and should not be used.

### Added

- **Stripe APM Module** (`checkout-stripe-apm`): Stripe Alternative Payment Methods via native PaymentSheet.
- **Braintree APM Module** (`checkout-braintree-apm`): Braintree PayPal and Venmo payments via native SDK.
- **Gateway-Specific 3DS via Chrome Custom Tabs**.

### Fixed

- Expanded terminal state detection to include `gateway_processing_result_unknown` and `gateway_setup_failed`.
- Thread safety in `GatewaySpecific3DSIntegration`.

## [0.10.1] - 2026-02-25

### Added

- **Stripe APM Integration**: Initial Stripe APM offsite payment integration with native PaymentSheet.
- **Braintree PayPal/Venmo Integration**.
- **Gateway-Specific 3DS Chrome Custom Tabs**.

## [0.10.0] - 2026-02-20

### Added

- **Offsite Payment Integration (SPREL)**: Chrome Custom Tabs-based offsite payment flow for SPREL and PayPal.
- **EBANX Payment Integration**: Support for Pix, Boleto, OXXO, and NuPay via EBANX.
- **Allow Blank Date Validation Parameter**.
- **Dark Mode Support**.
- **Gateway-Specific 3DS**: Initial Gateway-Specific 3DS feature.

### Fixed

- CVV clear behavior, combined expiry date validation, 3DS bottom sheet issues, recaching validation, status API fixes, CodeQL security findings.

## [0.7.1] - 2024-12-30

### Breaking Changes

Multi-module publishing replaces the single fat AAR.

**Old:** `implementation("com.spreedly:checkout-android:0.7.0")`

**New:**
```kotlin
implementation("com.spreedly:checkout-payments-core:0.7.1")
implementation("com.spreedly:checkout-hostedfields:0.7.1")
implementation("com.spreedly:checkout-paymentsheet:0.7.1")
```

### Added

- Multi-module publishing: `checkout-payments-core`, `checkout-hostedfields`, `checkout-paymentsheet`.
- Proper transitive dependency management.

## [0.7.0] - 2024-12-30

### Added

- **Save Card for Future Payments**: `shouldRetain` property and `SaveCardCheckbox` composable.
- **Focus Tracking Callback**: `onFocus` callback parameter to `SPLTextField` and `AppTextField`.

## [0.0.3] - 2025-08-25

### Added

- `AuthService` in app module for authentication parameter retrieval.
- Accessibility enhancements for all interactive UI components.

### Changed

- **Breaking**: SDK initialization now requires authentication parameters to be fetched by the app.

### Fixed

- Card number visual transformation cursor positioning bug.

## [0.0.1] - 2025-08-08

### Added

- Initial release of Spreedly Android SDK.
- Core payment processing with secure tokenization.
- Jetpack Compose UI components: hosted fields, payment sheet.
- Multi-module architecture: `payments-core`, `hostedfields`, `paymentsheet`.
- PCI-compliant payment field handling.
- Ktor-based HTTP client with certificate pinning.
