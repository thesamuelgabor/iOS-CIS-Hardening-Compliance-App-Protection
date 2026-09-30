# iOS CIS Hardening, Compliance & App Protection

## Objective

This project hardens `SG-iOS-ADE-CorporateOwned` (supervised, from [Apple Business Manager Setup](../06-Apple-Business-Manager-Setup) and [Comparing iOS Enrollment Methods](../07-iOS-Enrollment-Methods-Comparison)) against the CIS Apple iOS benchmark, builds a matching compliance policy, and adds an App Protection layer for `SG-iOS-BYOD-AccountDriven`.

### Skills Learned

- Device restriction profile design mapped to named CIS controls
- Compliance policy design feeding Conditional Access
- App Protection Policy (MAM) design independent of device enrollment

### Tools Used

- Microsoft Intune / Endpoint Manager admin center
- CIS Apple iOS Benchmark documentation

## Steps

#### 1. Device Restrictions Profile — CIS Mapped

| CIS Control | Description | Intune Setting | Value |
|---|---|---|---|
| 2.2 | Minimum passcode length | Minimum passcode length | 6 |
| 2.3 | Passcode complexity | Require alphanumeric passcode | Require |
| 2.9 | Failed attempt wipe | Maximum failed attempts | 6 |
| 3.4 | Restrict jailbroken devices | Jailbreak detection | Block |
| 4.1 | Disable Siri on lock screen | Siri while locked | Block |
| 5.2 | Restrict AirDrop | AirDrop | Block |

<img width="800" height="450" alt="image" src="docs/img/01-device-restrictions.png" />

*Ref 1: CIS-mapped restrictions*

#### 2. Compliance Policy

| Category | Setting | Value |
|---|---|---|
| Device Health | Jailbroken devices | Block |
| Device Properties | Minimum OS version | iOS 17.0 |
| System Security | Require passcode | Require, min length 6 |
| Noncompliance Actions | Mark noncompliant | Immediately |

<img width="800" height="450" alt="image" src="docs/img/02-compliance-policy.png" />

*Ref 2: Compliance policy*

#### 3. App Protection Policy for BYOD Devices

| Setting | Value |
|---|---|
| Prevent backup of corporate data | Yes |
| Restrict cut/copy/paste to managed apps | Yes |
| Require PIN for access | Yes, numeric, 6 digits |
| Wipe corporate data on unenroll | Yes |

Applied to `SG-iOS-BYOD-AccountDriven`, since those devices are never supervised and rely entirely on app-level protection.

<img width="800" height="450" alt="image" src="docs/img/03-app-protection.png" />

*Ref 3: App protection policy*

#### 4. Validate End to End

Confirmed the supervised ADE device shows Compliant with every CIS-mapped restriction enforced, and the BYOD device correctly blocks copy-paste of corporate data into personal apps.

<img width="800" height="450" alt="image" src="docs/img/04-validation.png" />

*Ref 4: End-to-end validation*

## About

iOS hardening built against named CIS Apple iOS benchmark controls, with compliance and App Protection layered on top of the enrollment methods from earlier in the iOS track.
